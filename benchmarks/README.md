# Amzqr Speed Benchmarks

基于 [pytest-benchmark](https://pytest-benchmark.readthedocs.io/) 的速度基准测试套件。
覆盖三类场景：纯二维码生成、静态图片合成、GIF 多帧合成。

## 运行方式

```sh
# 跑全部 benchmark（combine 类需单轮模式，否则极慢）
uv run pytest benchmarks/ --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001

# 保存基线快照（三类场景分开跑，避免混用轮数模式）
uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-autosave
uv run pytest benchmarks/test_bench_combine_static.py --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001 --benchmark-autosave
uv run pytest benchmarks/test_bench_combine_gif.py --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001 --benchmark-autosave

# 对比指定基线（按场景选择对应 ID）
uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-compare=0001
uv run pytest benchmarks/test_bench_combine_static.py --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001 --benchmark-compare=0002
uv run pytest benchmarks/test_bench_combine_gif.py --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001 --benchmark-compare=0003
```

## 基线记录

| 基线 ID | 场景 | 用例数 | 模式 | 对比命令 |
|----------|------|--------|------|----------|
| 0001 | ① QR 生成 | 32 | 多轮统计 | `--benchmark-compare=0001` |
| 0002 | ② 静态图片合成 | 96 | 单轮 | `--benchmark-compare=0002` |
| 0003 | ③ GIF 多帧合成 | 288 | 单轮 | `--benchmark-compare=0003` |

> 三个场景独立保存基线，对比时需指定对应 ID。
> 例如：优化 QR 生成后跑 `uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-compare=0001`
> 即可看到加速比。

## 已知限制

场景②③的 version 40 因 `combine()` 中 `putpixel` 嵌套循环极慢（单次 45 秒），已从 benchmark 参数中移除，待优化后恢复。
全量 608 用例中当前可跑 416 用例（32+96+288）。
combine 类 benchmark 使用 `--benchmark-min-rounds=1` 单轮模式以控制运行时间，QR 生成类使用默认多轮统计模式。
