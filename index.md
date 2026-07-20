# 🤖 คู่มือ Local LLM บน Mac M5 สำหรับ DevOps/SRE

**Device:** MacBook Air M5 | RAM 32GB | Apple Silicon

---

## 🧠 ทำไมต้องรัน LLM บนเครื่อง?

- 🔒 **Privacy** — โค้ด/ log/ config ไม่ออกจากเครื่อง
- ⚡ **Latency ต่ำ** — ไม่ต้องรอ API
- 💰 **ฟรี** — ไม่มีค่า API token
- 🛠️ **Offline** — ใช้ได้ทุกที่ ไม่ต้องต่อเน็ต
- 🎯 **Fine-tune ได้** — ปรับแต่งโมเดลสำหรับ environment ตัวเอง

---

## 💻 M5 + 32GB RAM — ทำอะไรได้บ้าง?

| Model Size | Quantization | RAM Usage | Speed |
|-----------|-------------|-----------|-------|
| 7B-8B | Q4_K_M | ~5-6 GB | ⚡⚡⚡ เร็วมาก (40+ tok/s) |
| 14B | Q4_K_M | ~9-10 GB | ⚡⚡ เร็ว (25+ tok/s) |
| 32B | Q4_K_M | ~20-22 GB | ⚡ พอใช้ (12+ tok/s) |
| 70B | IQ3_XXS | ~28-30 GB | 🐢 ช้า (4-6 tok/s) |

> 💡 **Recommendation:** 7B-14B สำหรับงานประจำวัน, 32B สำหรับงานที่ต้องการความแม่นยำสูง

---

## 📦 ติดตั้ง Ollama (แนะนำที่สุด)

Ollama รองรับ Apple Silicon แบบ native — ประสิทธิภาพดีที่สุดสำหรับมือใหม่

```bash
# ติดตั้ง Ollama
brew install ollama

# เริ่ม service (รันใน background)
ollama serve

# ทดสอบ — รันโมเดลแรก
ollama run llama3.2:3b
```

### คำสั่ง Ollama พื้นฐาน

```bash
ollama list              # ดูโมเดลที่ติดตั้ง
ollama pull <model>      # ดาวน์โหลดโมเดล
ollama run <model>       # รันโมเดล
ollama rm <model>        # ลบโมเดล
ollama ps                # ดูโมเดลที่กำลังรัน
ollama show <model>      # ดูรายละเอียดโมเดล
```

### Ollama API (ใช้ integrate กับเครื่องมืออื่น)

```bash
# HTTP API — รันที่ localhost:11434
curl http://localhost:11434/api/generate -d '{
  "model": "qwen2.5-coder:7b",
  "prompt": "เขียน Dockerfile สำหรับ Node.js app",
  "stream": false
}'
```

---

## 🎯 โมเดลแนะนำ แยกตาม Use Case

### 💻 Coding & Code Generation

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5 Coder** | 7B / 14B | `ollama pull qwen2.5-coder:14b` | 🏆 Best overall — เขียนโค้ด, refactor, generate |
| **DeepSeek Coder V2** | 16B | `ollama pull deepseek-coder-v2:16b` | อัลกอริทึมซับซ้อน, multi-file |
| **Codestral** | 22B | `ollama pull codestral:22b` | เติมโค้ด, autocomplete |
| **CodeGemma** | 7B | `ollama pull codegemma:7b` | เบา เร็ว — quick tasks |

### 🔍 Code Review

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5 Coder** | 14B | `ollama pull qwen2.5-coder:14b` | 🏆 Review โค้ด, หา bug, แนะนำ improvement |
| **DeepSeek Coder V2** | 16B | `ollama pull deepseek-coder-v2:16b` | deep analysis, security patterns |
| **Llama 3.1** | 8B | `ollama pull llama3.1:8b` | General review, อ่านได้หลายภาษา |

### 🔒 Security Scanning

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5 Coder** | 14B | `ollama pull qwen2.5-coder:14b` | หา SQL injection, XSS, hardcoded secrets |
| **DeepSeek Coder V2** | 16B | `ollama pull deepseek-coder-v2:16b` | วิเคราะห์ dependency, CVE patterns |
| **Llama 3.1** | 8B | `ollama pull llama3.1:8b` | หาช่องโหว่ใน config, IAM policy |

### ☸️ Kubernetes Analysis

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5** | 14B | `ollama pull qwen2.5:14b` | 🏆 วิเคราะห์ YAML, หา misconfiguration |
| **Llama 3.1** | 8B | `ollama pull llama3.1:8b` | อธิบาย error, แนะนำ troubleshooting |
| **Mistral** | 7B | `ollama pull mistral:7b` | เร็ว — quick diagnostics |

### 📊 Elastic / Grafana / Log Analysis

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5** | 14B | `ollama pull qwen2.5:14b` | 🏆 วิเคราะห์ log patterns, anomaly detection |
| **Llama 3.1** | 8B | `ollama pull llama3.1:8b` | สรุป error, แนะนำแก้ไข |
| **Mistral** | 7B | `ollama pull mistral:7b` | grep แบบ AI, หา correlation |

### 🧠 General DevOps Assistant

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 2.5** | 14B | `ollama pull qwen2.5:14b` | 🏆 All-around best สำหรับ DevOps |
| **Llama 3.1** | 8B | `ollama pull llama3.1:8b` | เร็ว เบา — everyday tasks |
| **Phi-4** | 14B | `ollama pull phi4:14b` | reasoning ดีมาก แม่นยำ |

---

## 🚀 One-liner ติดตั้งทุกอย่าง

```bash
# ติดตั้ง Ollama
brew install ollama

# ดาวน์โหลดโมเดลแนะนำ (เลือกตามต้องการ)
# ★★★ ต้องมี (Best all-rounder)
ollama pull qwen2.5-coder:14b

# ★★★ ต้องมี (General DevOps)
ollama pull qwen2.5:14b

# ★★ แนะนำ (สำรอง)
ollama pull llama3.1:8b
ollama pull deepseek-coder-v2:16b

# ★ ตัวเลือกเพิ่มเติม
ollama pull codestral:22b
ollama pull mistral:7b
ollama pull codegemma:7b
ollama pull phi4:14b
```

> ⚠️ **พื้นที่ดิสก์:** Qwen 2.5 Coder 14B (~9GB) + Qwen 2.5 14B (~9GB) + Llama 3.1 8B (~5GB) + DeepSeek Coder V2 16B (~10GB) = **~33GB รวม**

---

## 🔧 Integration กับ Tools

### VS Code + Continue.dev

Continue.dev เป็น extension ฟรีที่เชื่อม VS Code กับ Ollama:

```bash
# ติดตั้ง VS Code extension
code --install-extension Continue.continue
```

Config `~/.continue/config.json`:
```json
{
  "models": [
    {
      "title": "Qwen 2.5 Coder 14B",
      "provider": "ollama",
      "model": "qwen2.5-coder:14b"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Codestral 22B",
    "provider": "ollama",
    "model": "codestral:22b"
  }
}
```

### CLI + Shell Integration

```bash
# ฟังก์ชันสำหรับถาม AI จาก terminal
ai() {
  ollama run qwen2.5:14b "$@"
}

# ใช้วิเคราะห์ command output
kubectl describe pod problematic-pod | ai "วิเคราะห์ปัญหาจาก output นี้"

# ใช้ตรวจสอบ Terraform plan
terraform plan -no-color | ai "review terraform plan หา security issues"

# ใช้ตรวจสอบ Dockerfile
ai "review Dockerfile นี้หา security issues และ optimization: $(cat Dockerfile)"

# ใช้วิเคราะห์ logs
tail -100 /var/log/app.log | ai "สรุป errors และแนะนำวิธีแก้"
```

### Open WebUI (ChatGPT-like Interface)

```bash
# ติดตั้ง Open WebUI ด้วย Docker
docker run -d -p 3000:8080 \
  --name open-webui \
  -v open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:main

# หรือใช้ OrbStack (เบากว่า)
orbctl run -d -p 3000:8080 \
  --name open-webui \
  -v open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:main
```

จากนั้นเปิด `http://localhost:3000` — จะมี UI สวย ๆ แบบ ChatGPT ให้ใช้

---

## 📋 Use Case Examples

### 1. ตรวจสอบ Kubernetes Manifest

```bash
# ใช้ AI วิเคราะห์ manifest
cat deployment.yaml | ollama run qwen2.5:14b \
  "วิเคราะห์ Kubernetes manifest นี้:
   1. หา security issues (privileged containers, hostPath, etc.)
   2. หา resource limits ที่อาจทำให้เกิดปัญหา
   3. แนะนำ best practices ที่ควรปรับปรุง"
```

### 2. วิเคราะห์ Elasticsearch Logs

```bash
# ดึง logs แล้วให้ AI วิเคราะห์
curl -s "localhost:9200/_search?q=ERROR&size=10" | \
  ollama run qwen2.5:14b \
  "สรุป error patterns จาก Elasticsearch logs นี้
   และแนะนำวิธีการแก้ไข"
```

### 3. Code Review อัตโนมัติ

```bash
# Review PR diff
git diff main...feature-branch | ollama run qwen2.5-coder:14b \
  "Review code diff นี้:
   1. หา bugs และ logic errors
   2. หา security vulnerabilities
   3. แนะนำ performance improvements
   4. ตรวจสอบ naming conventions"
```

### 4. Security Scan IaC

```bash
# ตรวจสอบ Terraform
find . -name "*.tf" -exec cat {} + | ollama run qwen2.5:14b \
  "วิเคราะห์ Terraform code:
   1. S3 buckets — มี public access ไหม?
   2. Security groups — มี 0.0.0.0/0 inbound ไหม?
   3. IAM policies — overly permissive?
   4. Secrets — มี hardcoded credentials ไหม?"
```

### 5. Dockerfile Optimization

```bash
# วิเคราะห์ Dockerfile
ollama run qwen2.5-coder:14b "รีวิว Dockerfile นี้
  หา security issues และ optimization opportunities:
  $(cat Dockerfile)"
```

---

## ⚡ MLX — Apple Native Acceleration

MLX เป็น framework ของ Apple สำหรับรัน ML บน Apple Silicon — เร็วกว่า Ollama บางเคส

```bash
# ติดตั้ง MLX
pip install mlx mlx-lm

# รันโมเดลผ่าน MLX (เร็วกว่า Ollama สำหรับบางโมเดล)
mlx_lm.generate --model mlx-community/Qwen2.5-Coder-7B-4bit \
  --prompt "เขียนฟังก์ชัน Python สำหรับ validate Kubernetes YAML"

# รันเป็น server แบบ OpenAI API
mlx_lm.server --model mlx-community/Qwen2.5-Coder-7B-4bit
```

> 💡 **เมื่อไหร่ควรใช้ MLX:** สำหรับโมเดลที่ใช้บ่อยมาก ๆ และต้องการความเร็วสูงสุด  
> **เมื่อไหร่ควรใช้ Ollama:** สำหรับ general use — ง่ายกว่า, มี ecosystem ใหญ่กว่า

---

## 📊 Performance Benchmarks (M5, 32GB)

| โมเดล | Quant | Tokens/s | RAM | การใช้งาน |
|-------|-------|----------|-----|----------|
| Qwen 2.5 Coder 7B | Q4_K_M | ~45 tok/s | ~5 GB | ทั่วไป |
| Qwen 2.5 Coder 14B | Q4_K_M | ~28 tok/s | ~9 GB | **แนะนำ** |
| DeepSeek Coder V2 16B | Q4_K_M | ~22 tok/s | ~10 GB | งานหนัก |
| Codestral 22B | Q4_K_M | ~16 tok/s | ~14 GB | autocomplete |
| Qwen 2.5 32B | Q4_K_M | ~12 tok/s | ~21 GB | analysis ลึก |

---

## 🔐 Security Tips

- 🔒 **Air-gapped:** Ollama ใช้ offline ได้ 100% หลังจากดาวน์โหลดโมเดล
- 🚫 **ห้ามส่งโค้ด production เข้า cloud LLM** — ใช้ local เท่านั้นสำหรับ sensitive code
- 🔑 **ระวัง .env และ secrets** — อย่าหลุดเข้า prompt
- 📝 **Anonymize ก่อนส่ง** — ลบ IP, hostname, credential จาก log ก่อนส่งให้ AI
- 🛡️ **Ollama binds to localhost** — ไม่ expose สู่ network (default ปลอดภัย)

---

## 📁 Recommended Directory Setup

```bash
~/dev/
├── tools/
│   └── ai-scripts/          # AI helper scripts
│       ├── review-pr.sh     # Review PR diff
│       ├── scan-terraform.sh # Scan Terraform
│       ├── analyze-logs.sh   # Analyze logs
│       └── k8s-diagnose.sh   # K8s troubleshooting
└── prompts/
    ├── code-review.md        # Prompt template สำหรับ code review
    ├── security-scan.md      # Prompt template สำหรับ security scan
    └── k8s-analysis.md       # Prompt template สำหรับ K8s
```

---

## 🎯 Quick Start (5 นาที)

```bash
# 1. ติดตั้ง Ollama
brew install ollama

# 2. ดาวน์โหลดโมเดลหลัก
ollama pull qwen2.5-coder:14b
ollama pull qwen2.5:14b

# 3. ทดสอบ
ollama run qwen2.5-coder:14b "เขียน Dockerfile สำหรับ React app ที่ใช้ nginx"
```
