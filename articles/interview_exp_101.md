🧠 1️⃣ How do you find the top 5 CPU/Memory-consuming processes in Linux?

ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -n 6
Or use top / htop for live monitoring.

📜 2️⃣ How to check logs from the last 7 days?

sudo journalctl --since "7 days ago"
sudo find /var/log -type f -mtime -7
Use zcat or zgrep for older compressed logs.

⚙️ 3️⃣ Difference between Deployment and DaemonSet in Kubernetes
Deployment → Runs app replicas (e.g., Nginx, API).
DaemonSet → Runs one pod per node (e.g., log agent, node exporter).
💡 Deployments = scale apps. DaemonSets = node-level agents.

🔍 4️⃣ A Pod keeps crashing or evicting — how do you troubleshoot?
kubectl describe pod <pod> → check events
kubectl logs -p <pod> → previous crash logs
 Common causes → OOMKilled, Evicted, ImagePullBackOff, or probe failures.

🧱 5️⃣ What happens if Kubelet is not running?
Node marked NotReady
No new pods scheduled
Existing pods may still run
Image pulling & pod creation stop → Pods stay Pending

🔒 6️⃣ How do you restrict pod-to-pod communication?
Use NetworkPolicies to allow or deny traffic.
 They act like firewall rules inside your cluster 🔐

🌐 7️⃣ What is a Service Mesh & why do we use it?
A Service Mesh (Istio/Linkerd) manages all service-to-service communication for:
 ✅ mTLS encryption
 ✅ Canary & traffic routing
 ✅ Observability (metrics/tracing)
 ✅ Fault tolerance (retries, timeouts)

It works via sidecar proxies (Envoy) + a control plane managing configs & policies.

💬 My takeaway:
 The interview focused less on definitions and more on how you troubleshoot and reason through real issues — exactly what DevOps is about 💪
If you’re preparing for a DevOps/Kubernetes interview, save this post — these are real-world scenarios worth mastering!
