# Architecture: Golden Path

The first deployable unit is a stateless image worker. It accepts one document-page image and returns a processed image plus a JSON trace.

```text
image upload
  -> OpenCV evidence collector
  -> tool-calling quality-gate agent
  -> correction / rescan request / human-review event
  -> re-analysis when corrected
  -> immutable trace record
```

## Safety boundaries

- `content_touches_frame` is an escalation signal, not a claim that a physical page boundary was found.
- Only confident small skew is automatically corrected in the MVP.
- Blur requests a rescan; low contrast, weak skew evidence, frame-edge content, and failed verification go to human review.
- The trace contains checksums and version fields, but never persists the document image itself.

## AWS deployment target

1. Store raw and processed images in separate S3 prefixes.
2. Invoke an ARM64 worker on AWS Graviton.
3. Store trace metadata in DynamoDB and metrics in CloudWatch.
4. Use Step Functions to route rescan and human-review events.

The COOL award path remains conditional: the core workload must be run with a verified COOL installation on the Graviton path and compared to a baseline on the same hardware.
