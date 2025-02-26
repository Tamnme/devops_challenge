## My NGINX Loadbalancing VM on Cloud:
- 64Gb Storage
- Symtom: A consistently high memory usage (99%)
## Troubleshoot:
### 1. System check
- Use `top` or `htop` to check resources usage of running processes
- Use `free -h` to check RAM and swap usage. High swap usage indicates memory pressure.
- Check Swap usage: If swap is overused, it may indicate memory exhaustion
```
swapon -s
```
### 2. Check Nginx
- Run command to list all processes related to NGINX and check the memory usage (the %MEM column).
```
ps aux | grep nginx
```
- List & Check NGINX process Memory usage
```
ps -eo pid,comm,%mem,%cpu --sort=-%mem | grep nginx
```
- Check NGINX Log for anomalies
- Access logs
```
tail -f /var/log/nginx/access.log
```
- Error logs
```
tail -f /var/log/nginx/error.log
```
## Possible Root Cause & Solution
| Root Cause                               | Impact                                                                                    | Recovery Steps                                                                                                                                                                                                                                                               |
| ----------------------------------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Too Many Concurrent Connections (High Traffic Spike)** | Memory usage spikes as NGINX holds connections, Performance degradation and increased latency, OOM kill | Reduce Keepalive Timeout, Limit Connections Per Client, Enable Rate Limiting in `nginx.conf`, Mitigate DDoS by using `Cloudflare` or `WAF` |
| **NGINX Worker Processes Consuming Excessive Memory** | Increased memory consumption(OOM kill), potential performance degradation(response time). | Adjust `worker_processes`, `Reduce Buffer Size` in `nginx.conf`. Start with a number close to the number of CPU cores.  Restart NGINX (`systemctl restart nginx`).|
| **NGINX Logging Consuming Excessive Memory** | Excessive memory usage by the cache, High I/O usage and potential disk swapping | Disable Debug Logging, Rotate Logs, Review NGINX caching configuration. Ensure cache size limits are appropriate. Consider using a separate caching server (e.g., Redis, Memcached). Restart NGINX.|
| **Memory leak (NGINX or library)** | Memory usage continuously increases until the system becomes unstable. | Identify the leaky component (difficult). Upgrade and Restart NGINX |
| **OS-Level Issues(cron job, background processes)** | Uncontrolled resource consumption by the cron jobs, background processes. Overall system performance degrades. | Identify unnessary crob jobs and background processes. Correct its behavior or disable it.|

## Summary
1. Immediate Fix
- Restart NGINX:
```
systemctl restart nginx
```
- Check logs and top for memory consumers.
- Reduce keepalive and buffer sizes.
2. If It Persists
- Tune worker processes and apply rate limiting.
- Check for memory leaks and update NGINX.
3. For Long-Term Stability
- Implement log rotation.
- Monitor memory usage with htop or nginx-status.
- Upgrade NGINX if needed.
