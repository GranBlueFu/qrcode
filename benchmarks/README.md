# Amzqr Speed Benchmarks

基于 [pytest-benchmark](https://pytest-benchmark.readthedocs.io/) 的速度基准测试套件。
覆盖三类场景：纯二维码生成、静态图片合成、GIF 多帧合成。

## 运行方式

```sh
# 按场景分别运行（模式因场景而异：QR 生成与静态图片合成为多轮统计，
# 走 pytest-benchmark 默认多轮；GIF 多帧合成为单轮模式以控时，
# 勿用单条命令混跑全部，否则 GIF 会落在默认多轮模式）
uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-only
uv run pytest benchmarks/test_bench_combine_static.py --benchmark-only
uv run pytest benchmarks/test_bench_combine_gif.py --benchmark-only --benchmark-min-rounds=1 --benchmark-min-time=0.000001

# 保存基线快照（三个场景分别保存，便于对比）
uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-autosave
uv run pytest benchmarks/test_bench_combine_static.py --benchmark-autosave
uv run pytest benchmarks/test_bench_combine_gif.py --benchmark-min-rounds=1 --benchmark-min-time=0.000001 --benchmark-autosave

# 对比指定基线（按场景选择对应 ID）
uv run pytest benchmarks/test_bench_qr_generation.py --benchmark-compare=0001
uv run pytest benchmarks/test_bench_combine_static.py --benchmark-compare=0004
uv run pytest benchmarks/test_bench_combine_gif.py --benchmark-compare=0006 --benchmark-min-rounds=1 --benchmark-min-time=0.000001
```

## 基线记录

| 基线 ID | 场景 | 用例数 | 模式 | 对比命令 |
|----------|------|--------|------|----------|
| 0001 | ① QR 生成 | 32 | 多轮统计 | `--benchmark-compare=0001` |
| 0004 | ② 静态图片合成 | 144 | 多轮统计 | `--benchmark-compare=0004` |
| 0006 | ③ GIF 多帧合成 | 432 | 单轮 | `--benchmark-compare=0006` |

> 三个场景独立保存基线，对比时需指定对应 ID。
> ②③ 的基线为 #14 优化后重建（替代优化前因慢速而设的单轮基线），覆盖 version 1/10/40。
> 静态图片合成使用多轮统计（#14 后已足够快）；GIF 多帧合成本质较慢，回归基线采用
> 单轮完整模式以保证完整性与可执行性，静态仍为多轮统计。0002/0003 是优化前的旧基线，
> 仅供演示 #14 加速；作为长期回归基准的是重建后的 0004/0006。
