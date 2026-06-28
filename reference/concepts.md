# PyTorch 小白概念速查

讲解时**用自己的话、结合当前代码**,不要整段照抄。下面是参考底稿。

---

## 张量 Tensor 基础

- **Tensor** = 带维度的多维数组,像 NumPy 的 ndarray,但能跑在 GPU 上、能自动求导。
- **shape(形状)**:`x.shape` 看每一维有多少个元素,如 `[B, T, D]`。看不懂代码先 `print(x.shape)`。
- **dtype(数据类型)**:`torch.float32`(默认,训练用)、`torch.long`(整数,常做分类标签/索引)。类型不对会报 `expected scalar type ...`。
- **device(设备)**:`cpu` 或 `cuda`(GPU)。`x.to(device)` 搬设备。**张量和模型必须在同一设备**,否则报错。

## autograd 自动求导(深度学习的核心)

- 训练 = 让模型参数朝「让 loss 变小」的方向调整。怎么知道方向?对每个参数求**梯度(导数)**。
- PyTorch 会自动记录张量运算,构成**计算图**;`loss.backward()` 沿图反向算出所有梯度,存到每个参数的 `.grad` 里。
- `requires_grad=True` 的张量才会被追踪(模型参数默认就是)。
- 直觉:`backward()` 负责「算梯度」,`optimizer.step()` 负责「用梯度更新参数」。两步是分开的。

## nn.Module 与 forward

- 自定义模型继承 `nn.Module`,在 `__init__` 里**定义有哪些层**,在 `forward` 里**定义数据怎么流过这些层**。
- 调用模型用 `model(x)`,**不要**直接写 `model.forward(x)`——前者会触发 PyTorch 必要的内部钩子。
- `nn.Linear(in, out)`、`nn.Conv2d`、`nn.Embedding` 等都是层;它们内部的权重就是要训练的参数。

## 训练循环三步(顺序不能乱)

```python
optimizer.zero_grad()   # 1. 清空上一轮的梯度(梯度默认会累加,不清会越加越错)
loss = criterion(model(x), y)
loss.backward()         # 2. 反向传播,算出每个参数的梯度,填到 .grad
optimizer.step()        # 3. 按梯度更新参数(真正"学习"发生在这一步)
```
- 最常见的 bug:**忘了 `zero_grad()`**,梯度累积导致训练发散/异常。
- 进阶:故意不每步清零、隔几步再 step,叫「梯度累积」,用来用小显存模拟大 batch。

## no_grad / inference_mode(推理/验证时)

- 验证或预测时**不需要梯度**。包在 `with torch.no_grad():` 里,可省显存、提速。
- `torch.inference_mode()` 是更彻底的版本,纯推理时更推荐。

## train() / eval() 模式

- `model.train()` vs `model.eval()` 会改变 **Dropout** 和 **BatchNorm** 的行为。
- 验证/测试前**一定要 `model.eval()`**,训练前切回 `model.train()`。忘了切是经典 bug(验证指标莫名其妙)。

## 损失函数常见坑

- `nn.CrossEntropyLoss` **内部已经包含 softmax**:喂给它**原始 logits**,不要自己先 softmax。
- 它要的标签是**类别索引**(`long` 类型,形状 `[B]`),不是 one-hot。
- `reduction='mean'`(默认)会对 batch 求平均;算总和/逐样本时才改。

## 广播 Broadcasting

- 形状不同的张量做运算时,PyTorch 会自动「广播」对齐(从最后一维往前比,维度相等或其中一个为 1 就能对齐)。
- 好用但危险:形状没对齐却"恰好"能广播,会算出错误结果还不报错。不确定就显式 `unsqueeze`/`view` 对齐,并打印形状确认。

## detach / item / numpy

- `.item()`:把**只含一个数**的张量变成 Python 数字(常用于打印 loss:`loss.item()`)。
- `.detach()`:从计算图里「摘下来」,得到不带梯度的张量(存指标、做不需要反传的计算时用)。
- 转 NumPy:`x.detach().cpu().numpy()`(先脱离梯度图、再搬回 CPU)。

## in-place 操作(带下划线的坑)

- 末尾带 `_` 的方法是**原地修改**:`x.add_(1)`、`x.relu_()`、`x[mask] = 0`。
- 风险:可能破坏 autograd 需要的中间值,报 `a leaf Variable that requires grad is being used in an in-place operation` 之类。新手优先用非原地版本(`y = x + 1`)。

---

## 常见报错速查

| 报错关键词 | 多半是因为 | 怎么查 |
|---|---|---|
| `size mismatch` / `shapes cannot be multiplied` | 张量形状对不上(尤其 Linear/matmul) | 在出错行前 `print(x.shape)` 逐步对 |
| `Expected all tensors to be on the same device` | 张量和模型一个在 CPU 一个在 GPU | 检查 `.to(device)` 是否都做了 |
| `expected scalar type Long but found Float` | dtype 不对(分类标签要 `long`) | `y = y.long()` / `x = x.float()` |
| `element 0 ... does not require grad` | 对不带梯度的张量 `backward()` | 检查是否误用了 `no_grad`/`detach` |
| `Trying to backward ... second time` | 同一计算图反传了两次 | 通常是循环里漏了重建图,或要 `retain_graph` |
| loss 不下降 / 是 nan | 学习率太大、忘了 `zero_grad`、数据没归一化 | 调小 lr、查训练三步、打印中间值 |

---

## 新手十大坑(可作 checklist)

1. 忘了 `optimizer.zero_grad()` → 梯度累积。
2. 验证/测试忘了 `model.eval()` 和 `torch.no_grad()`。
3. 张量和模型不在同一 `device`。
4. 给 `CrossEntropyLoss` 喂了已经 softmax 的值,或标签用了 one-hot/float。
5. 形状没对齐,被广播"悄悄"算错。
6. 用 `.item()` 之外的方式把张量塞进 Python list,导致显存泄漏(累积计算图)——存指标用 `.item()` 或 `.detach()`。
7. dtype 用错(标签该 `long`、输入该 `float`)。
8. 直接 `model.forward(x)` 而不是 `model(x)`。
9. in-place 操作破坏了反向传播。
10. 学习率没调,默认就跑,loss 爆炸或不动。
