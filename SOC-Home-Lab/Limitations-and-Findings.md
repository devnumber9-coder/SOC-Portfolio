## Limitations and Findings

During testing, Windows account lockout controls limited the number of consecutive failed authentication attempts within a single time window.

As a result:
- Failed attempts were split across time buckets
- Alert thresholds were not met in a single scheduled run

This behavior reflects real-world endpoint protections and highlights why SOC teams often rely on dashboards and trend analysis rather than strict alert thresholds for authentication-based detections.
