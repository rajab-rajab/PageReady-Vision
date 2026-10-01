# Evaluation Protocol

## Dataset split

Use 150 non-sensitive, synthetic pages with a checked-in manifest:

- 50 calibration pages: threshold tuning only.
- 100 holdout pages: do not inspect when changing thresholds or policy.

Each manifest entry records its source template, corruption seed, intended defect labels, expected policy action, and SHA-256 checksum.

## Metrics

Report results per defect type and severity, not only as a single aggregate:

- precision and recall for blur, frame-edge-content, and low-contrast escalation;
- skew absolute error over the validated small-rotation range;
- correction verification success rate;
- unsafe approval count (must be zero in the holdout set);
- latency, throughput, and measured AWS cost.

## Benchmarking rule

Measure vanilla OpenCV and COOL on the same AWS Graviton deployment configuration and the same fixed inputs. Report the runtime version, instance type, image dimensions, iterations, warm-up procedure, and raw measurements. Never infer COOL performance from an x86-versus-Arm comparison.
