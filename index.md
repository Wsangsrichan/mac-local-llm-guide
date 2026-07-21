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
ollama run gemma4:12b
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
  "model": "qwen3.6:27b",
  "prompt": "เขียน Dockerfile สำหรับ Node.js app",
  "stream": false
}'
```


---

## ❓ Ollama vs vLLM — ทำไมไม่ใช้ vLLM?

vLLM เป็นเครื่องมือที่ยอดเยี่ยมสำหรับ **production serving บน GPU server** — แต่ออกแบบมาสำหรับ NVIDIA CUDA เท่านั้น:

| | **vLLM** | **Ollama** |
|---|---------|----------|
| Apple Silicon (M1-M5) | ❌ ไม่รองรับ (ต้องใช้ CUDA) | ✅ Native Metal acceleration |
| NVIDIA GPU | ✅ ดีที่สุด | ✅ รองรับ |
| Continuous batching | ✅ | ❌ (ไม่จำเป็นสำหรับ local) |
| Production throughput | ✅ สูงมาก | ❌ |
| ติดตั้งบน Mac | ❌ ซับซ้อนมาก / แทบใช้ไม่ได้ | ✅ `brew install ollama` |
| เหมาะกับ | Data center, GPU cluster | 🏆 **Local development บน Mac** |

> 💡 **สรุป:** vLLM สำหรับ server, Ollama สำหรับ local dev — ใช้คนละสถานการณ์กัน ถ้าคุณรันบน Mac → Ollama คือคำตอบ

---

## 🎯 โมเดลแนะนำ แยกตาม Use Case

### 💻 Coding & Code Generation

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | 🏆 Best overall — เขียนโค้ด, refactor, generate, agentic coding |
| **DeepSeek R1** | 32B | `ollama pull deepseek-r1:32b` | อัลกอริทึมซับซ้อน, multi-file, deep reasoning |
| **Mistral Small 3.2** | 24B | `ollama pull mistral-small3.2:24b` | เติมโค้ด, autocomplete, instruction-following |
| **Granite 4.1** | 30B | `ollama pull granite4.1:30b` | coding/reasoning แข็งแกร่งจาก IBM |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | เบา เร็ว — quick tasks, agentic workflows |

### 🔍 Code Review

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | 🏆 Review โค้ด, หา bug, แนะนำ improvement |
| **DeepSeek R1** | 32B | `ollama pull deepseek-r1:32b` | deep analysis, security patterns, reasoning |
| **Granite 4.1** | 30B | `ollama pull granite4.1:30b` | reasoning ดีมาก — หา logic bugs |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | General review, อ่านได้หลายภาษา |

### 🔒 Security Scanning

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | หา SQL injection, XSS, hardcoded secrets |
| **DeepSeek R1** | 32B | `ollama pull deepseek-r1:32b` | วิเคราะห์ dependency, CVE patterns |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | หาช่องโหว่ใน config, IAM policy |

### ☸️ Kubernetes Analysis

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | 🏆 วิเคราะห์ YAML, หา misconfiguration |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | อธิบาย error, แนะนำ troubleshooting |
| **Nemotron 3** | 33B | `ollama pull nemotron3:33b` | เร็ว — quick diagnostics, reasoning |

### 📊 Elastic / Grafana / Log Analysis

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | 🏆 วิเคราะห์ log patterns, anomaly detection |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | สรุป error, แนะนำแก้ไข |
| **Mistral Small 3.2** | 24B | `ollama pull mistral-small3.2:24b` | grep แบบ AI, หา correlation |

### 🧠 General DevOps Assistant

| โมเดล | Size | คำสั่งติดตั้ง | เหมาะกับ |
|-------|------|-------------|---------|
| **Qwen 3.6** | 27B | `ollama pull qwen3.6:27b` | 🏆 All-around best สำหรับ DevOps |
| **Gemma 4** | 12B | `ollama pull gemma4:12b` | เร็ว เบา — everyday tasks |
| **Granite 4.1** | 30B | `ollama pull granite4.1:30b` | ทดแทน GPT-4 ได้ดีบน local |
| **Nemotron 3** | 33B | `ollama pull nemotron3:33b` | reasoning ดีมาก แม่นยำ — NVIDIA |

---

## 🚀 One-liner ติดตั้งทุกอย่าง

```bash
# ติดตั้ง Ollama
brew install ollama

# ดาวน์โหลดโมเดลแนะนำ (เลือกตามต้องการ)
# ★★★ ต้องมี (Best all-rounder — Coding + DevOps)
ollama pull qwen3.6:27b

# ★★★ ต้องมี (Deep reasoning + Code review)
ollama pull deepseek-r1:32b

# ★★ แนะนำ (เบา เร็ว — everyday tasks)
ollama pull gemma4:12b

# ★★ แนะนำ (Code autocomplete + instruction-following)
ollama pull mistral-small3.2:24b

# ★ ตัวเลือกเพิ่มเติม
ollama pull granite4.1:30b
ollama pull nemotron3:33b
```

> ⚠️ **พื้นที่ดิสก์:** Qwen 3.6 27B (~16GB) + DeepSeek R1 32B (~20GB) + Gemma 4 12B (~8GB) + Mistral Small 3.2 24B (~15GB) = **~59GB รวม** (แนะนำติดตั้งเฉพาะที่จำเป็น)

---

## 🔧 Integration กับ Tools

### VS Code + Continue.dev

Continue.dev เป็น extension ฟรีที่เชื่อม VS Code กับ Ollama (เวอร์ชันล่าสุด mid-2026):

```bash
# ติดตั้ง VS Code extension
code --install-extension Continue.continue
```

Config `~/.continue/config.json` (ใช้ format ล่าสุด 2026):
```json
{
  "models": [
    {
      "title": "Qwen 3.6 27B",
      "provider": "ollama",
      "model": "qwen3.6:27b",
      "roles": ["chat", "autocomplete"]
    },
    {
      "title": "DeepSeek R1 32B",
      "provider": "ollama",
      "model": "deepseek-r1:32b",
      "roles": ["chat"]
    }
  ],
  "tabAutocompleteModel": {
    "title": "Mistral Small 3.2 24B",
    "provider": "ollama",
    "model": "mistral-small3.2:24b"
  },
  "embeddingsProvider": {
    "provider": "ollama",
    "model": "nomic-embed-text"
  }
}
```

### CLI + Shell Integration

```bash
# ฟังก์ชันสำหรับถาม AI จาก terminal
ai() {
  ollama run qwen3.6:27b "$@"
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
cat deployment.yaml | ollama run qwen3.6:27b \
  "วิเคราะห์ Kubernetes manifest นี้:
   1. หา security issues (privileged containers, hostPath, etc.)
   2. หา resource limits ที่อาจทำให้เกิดปัญหา
   3. แนะนำ best practices ที่ควรปรับปรุง"
```

### 2. วิเคราะห์ Elasticsearch Logs

```bash
# ดึง logs แล้วให้ AI วิเคราะห์
curl -s "localhost:9200/_search?q=ERROR&size=10" | \
  ollama run qwen3.6:27b \
  "สรุป error patterns จาก Elasticsearch logs นี้
   และแนะนำวิธีการแก้ไข"
```

### 3. Code Review อัตโนมัติ

```bash
# Review PR diff
git diff main...feature-branch | ollama run qwen3.6:27b \
  "Review code diff นี้:
   1. หา bugs และ logic errors
   2. หา security vulnerabilities
   3. แนะนำ performance improvements
   4. ตรวจสอบ naming conventions"
```

### 4. Security Scan IaC

```bash
# ตรวจสอบ Terraform
find . -name "*.tf" -exec cat {} + | ollama run qwen3.6:27b \
  "วิเคราะห์ Terraform code:
   1. S3 buckets — มี public access ไหม?
   2. Security groups — มี 0.0.0.0/0 inbound ไหม?
   3. IAM policies — overly permissive?
   4. Secrets — มี hardcoded credentials ไหม?"
```

### 5. Docker/Container Analysis

```bash
# วิเคราะห์ Dockerfile
ollama run qwen3.6:27b "รีวิว Dockerfile นี้
  หา security issues และ optimization opportunities:
  $(cat Dockerfile)"

# วิเคราะห์ docker-compose
ollama run qwen3.6:27b "วิเคราะห์ docker-compose.yml:
  $(cat docker-compose.yml)
  1. หา security issues (privileged mode, host mounts)
  2. ตรวจสอบ network configuration
  3. แนะนำ optimization"
```

### 6. 🤖 AI Agent Pipeline — Local Agent อัตโนมัติ

```bash
# สร้าง AI agent ที่ทำงานอัตโนมัติผ่าน shell
# ตัวอย่าง: agent ตรวจสอบ health check และแจ้งเตือน

cat << 'EOF' > /tmp/agent-health.sh
#!/bin/bash
# AI Agent: ตรวจสอบ service health และแนะนำวิธีแก้

ENDPOINTS=("http://localhost:8080/health" "http://localhost:3000/api/status")

for url in "${ENDPOINTS[@]}"; do
  response=$(curl -s -o /dev/null -w "%{http_code}" "$url" 2>/dev/null)
  if [ "$response" != "200" ]; then
    echo "❌ $url → HTTP $response"
    echo "วิเคราะห์โดย AI..."
    ollama run qwen3.6:27b "Service health check failed at $url (HTTP $response).
      Suggest troubleshooting steps and possible root causes for a DevOps engineer."
  fi
done
EOF
chmod +x /tmp/agent-health.sh
```

### 7. 📝 Meeting Summary & Document Generation

```bash
# สรุปบันทึกการประชุมจาก transcript
ollama run qwen3.6:27b "สรุปบันทึกการประชุมจาก transcript นี้:
  $(cat meeting-transcript.txt)
  
  กรุณาสรุป:
  1. หัวข้อหลักที่พูดคุย
  2. ข้อตัดสินใจ (Decisions)
  3. Action items พร้อมผู้รับผิดชอบ
  4. สรุปเป็น bullet points ภาษาไทย"

# สร้าง RCA (Root Cause Analysis) document จาก incident log
ollama run qwen3.6:27b "จาก incident log นี้ สร้าง RCA document:
  $(cat incident.log)
  
  ประกอบด้วย:
  - Timeline ของเหตุการณ์
  - Root cause
  - Impact analysis
  - Preventive measures"
```

### 8. 🧪 Generate Unit Tests

```bash
# สร้าง unit tests จาก source code
ollama run qwen3.6:27b "สร้าง unit tests สำหรับฟังก์ชันนี้:
  \`\`\`python
  $(cat src/auth.py)
  \`\`\`
  ใช้ pytest framework, ครอบคลุม:
  - Happy path
  - Edge cases
  - Error handling
  - Mock external dependencies"

# สร้าง test ทั้งไฟล์พร้อมวัด coverage
find src/ -name "*.py" | while read f; do
  echo "=== Generating tests for $f ==="
  ollama run qwen3.6:27b "Generate comprehensive pytest unit tests for:
    \`\`\`python
    $(cat "$f")
    \`\`\`" > "tests/test_$(basename $f)"
done
```

### 9. 🔄 Multi-Model Pipeline — หลายโมเดลทำงานต่อกัน

```bash
# Pipeline: Code → Review → Fix → Security Scan
# ใช้หลายโมเดลทำงานต่อเนื่องกัน

CODE_FILE="src/main.py"

# Step 1: Qwen 3.6 — Code Review
REVIEW=$(ollama run qwen3.6:27b "Review this code for bugs and issues:
  \`\`\`python
  $(cat $CODE_FILE)
  \`\`\`")

echo "=== Code Review ==="
echo "$REVIEW"

# Step 2: DeepSeek R1 — Deep Reasoning & Logic Check
LOGIC_CHECK=$(ollama run deepseek-r1:32b "Analyze the logic and edge cases:
  \`\`\`python
  $(cat $CODE_FILE)
  \`\`\`")

echo "=== Logic Analysis ==="
echo "$LOGIC_CHECK"

# Step 3: Gemma 4 — Security Scan (เร็ว)
SECURITY_SCAN=$(ollama run gemma4:12b "Scan for security vulnerabilities:
  \`\`\`python
  $(cat $CODE_FILE)
  \`\`\`")

echo "=== Security Scan ==="
echo "$SECURITY_SCAN"
```

### 10. 🚀 Shell Autocomplete — AI ช่วยเติม Command

```bash
# AI-powered shell autocomplete
# เพิ่มใน ~/.bashrc หรือ ~/.zshrc

_ai_complete() {
  local partial="${COMP_LINE}"
  local suggestion=$(ollama run gemma4:12b \
    "Suggest a complete shell command. Current partial: '$partial'.
    Reply with ONLY the completed command, no explanation." 2>/dev/null)
  COMPREPLY=("$suggestion")
}

# เปิดใช้งานด้วย Ctrl+F (หรือ bind ตามที่ต้องการ)
bind -x '"\C-f": _ai_complete'

# ตัวอย่างการใช้งาน:
# พิมพ์ "kubectl get po" แล้วกด Ctrl+F
# AI จะแนะนำ: "kubectl get pods --all-namespaces | grep Error"
```

---

## 🤖 Hermes Agent + Local LLM

### Hermes Agent คืออะไร

**Hermes Agent** เป็น open-source AI agent framework ที่พัฒนาโดย [Nous Research](https://hermes-agent.nousresearch.com/) — รันบน terminal ได้เต็มรูปแบบ สามารถเชื่อมต่อกับ local LLM ผ่าน Ollama เพื่อทำงานอัตโนมัติแบบ Agentic (คิด → ตัดสินใจ → ลงมือทำ → ตรวจสอบผลลัพธ์)

แตกต่างจากการเรียก LLM แบบธรรมดา (ถาม→ตอบ) เพราะ Hermes มีความสามารถ:
- 🛠️ **Tools** — ใช้ shell, อ่าน/เขียนไฟล์, เข้าถึงระบบ ได้โดยตรง
- 🔗 **Multi-step reasoning** — คิดเป็นขั้นตอน แก้ปัญหาซับซ้อน
- 🔄 **Self-correcting** — ตรวจสอบผลลัพธ์ตัวเอง แก้ไขถ้าผิดพลาด
- 📅 **Scheduled tasks** — ทำงานตามเวลา (cron)
- 🧩 **Extensible** — เพิ่ม custom tools/plugins ได้ตามต้องการ

> 💡 **TL;DR:** Hermes = AI ที่ไม่ใช่แค่ตอบคำถาม แต่ **ทำงานให้คุณได้จริง ๆ** — review PR, วิเคราะห์ logs, ตรวจสอบ security, แจ้งเตือนอัตโนมัติ

---

### วิธีตั้งค่า Hermes + Local LLM

```bash
# 1. ติดตั้ง Hermes Agent
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# 2. ตั้งค่าให้ใช้ Ollama เป็น provider (local LLM)
hermes config set provider ollama
hermes config set model qwen3.6:27b

# 3. ตรวจสอบการตั้งค่า
hermes config show

# 4. ทดสอบ Hermes
hermes "เขียน script bash สำหรับ backup PostgreSQL database ทุกวัน"
```

#### ตั้งค่าโมเดลอื่นตาม Use Case

```bash
# Deep reasoning + โค้ดซับซ้อน → DeepSeek R1
hermes config set model deepseek-r1:32b

# งานเร็ว เบา → Gemma 4
hermes config set model gemma4:12b

# Best all-rounder → Qwen 3.6 (แนะนำเริ่มต้น)
hermes config set model qwen3.6:27b
```

---

### Use Cases — Hermes + Local LLM สำหรับ DevOps

#### 🔄 1. Automated PR Review

Hermes อ่าน PR diff → local LLM review → post comment อัตโนมัติ:

```bash
# Review PR และโพสต์ comment
hermes "อ่านไฟล์ที่เปลี่ยนแปลงใน PR นี้:
  \`\`\`diff
  $(git diff main...feature-branch)
  \`\`\`
  ตรวจสอบ: bugs, security issues, performance problems, naming conventions
  แล้วสรุปเป็น review comment แบบกระชับ เป็นภาษาไทย"
```

#### 📊 2. Log Analysis Agent

Hermes อ่าน logs → วิเคราะห์ → แจ้งเตือนอัตโนมัติ:

```bash
# วิเคราะห์ logs แบบ real-time
tail -f /var/log/app.log | while read line; do
  echo "$line" | hermes "วิเคราะห์ log บรรทัดนี้
    ถ้าเป็น ERROR/CRITICAL → สรุปปัญหา แนะนำวิธีแก้
    ถ้าปกติ → ไม่ต้องตอบอะไร" >> /tmp/ai-log-report.txt
done
```

#### 🚀 3. Deployment Assistant

Hermes ช่วยตรวจสอบก่อน deploy (checklist, health check):

```bash
# Pre-deployment checklist
hermes "นี่คือ deployment plan สำหรับ production:
  \`\`\`yaml
  $(cat deploy-plan.yaml)
  \`\`\`
  ตรวจสอบ:
  1. Rollback strategy พร้อมหรือไม่
  2. Health check endpoints ถูกต้องไหม
  3. Environment variables ครบถ้วนไหม
  4. Database migration — มี breaking changes ไหม
  5. มี single point of failure ไหม"
```

#### 🐳 4. Docker/K8s Troubleshooting

Hermes อ่าน error → วิเคราะห์ root cause → แนะนำแก้ไข:

```bash
# วิเคราะห์ Kubernetes error
kubectl describe pod broken-pod | hermes "วิเคราะห์ pod นี้:
  - หา root cause ของปัญหา
  - แนะนำวิธีแก้ไขทีละขั้นตอน
  - ระบุว่าควรตรวจสอบอะไรเพิ่มเติม"

# วิเคราะห์ Docker build failure
docker build . 2>&1 | hermes "วิเคราะห์ Docker build error นี้
  แนะนำวิธีแก้ไข และ optimization opportunities"
```

#### 📝 5. Daily Standup Generator

Hermes อ่าน git log → สรุปงานประจำวัน:

```bash
# สร้าง daily standup จาก git history
git log --since="1 day ago" --oneline | hermes "จาก git log นี้
  สร้าง daily standup report (ภาษาไทย):
  1. What I did yesterday
  2. What I plan to do today
  3. Blockers (ถ้ามี)"
```

#### 🔐 6. Security Audit Pipeline

Hermes สแกน IaC → local LLM วิเคราะห์ → report:

```bash
# สแกน Terraform security
find . -name "*.tf" -exec cat {} + | hermes "วิเคราะห์ Terraform code:
  1. S3 buckets — public access? encryption?
  2. Security groups — 0.0.0.0/0 inbound?
  3. IAM policies — overly permissive?
  4. Secrets — hardcoded credentials?
  5. สรุปความเสี่ยง เรียงตาม severity สูง→ต่ำ"

# สแกน Dockerfile security
hermes "ตรวจสอบ Dockerfile นี้ตาม CIS Docker Benchmark:
  \`\`\`dockerfile
  $(cat Dockerfile)
  \`\`\`
  รายงานเฉพาะ issues ที่พบ พร้อมวิธีแก้ไข"
```

#### 🧪 7. Test Generation

Hermes อ่าน source → สร้าง unit tests:

```bash
# สร้าง unit tests อัตโนมัติ
hermes "อ่าน source code นี้และสร้าง comprehensive unit tests:
  \`\`\`python
  $(cat src/api_handler.py)
  \`\`\`
  ใช้ pytest framework ครอบคลุม:
  - Happy path, edge cases, error handling
  - Mock external dependencies
  - Parameterized tests สำหรับ input หลายรูปแบบ"
```

#### ⏰ 8. Scheduled Tasks (Cron Jobs)

Hermes cron + local LLM สำหรับงานประจำ:

```bash
# ตรวจสอบ health ทุก 30 นาที
hermes cron create \
  --name "health-check" \
  --schedule "30m" \
  --prompt "curl http://localhost:8080/health และ http://localhost:3000/api/status
    ถ้า endpoint ไหนไม่ตอบ 200 → วิเคราะห์สาเหตุและแจ้งเตือน"

# สร้าง report ทุกวัน 9:00 น.
hermes cron create \
  --name "daily-report" \
  --schedule "24h" \
  --at "09:00" \
  --prompt "อ่าน logs จากเมื่อวานจาก /var/log/app/*
    สรุป: errors ที่พบ, throughput, response time trends
    แนะนำสิ่งที่ควรปรับปรุง"

# ดู cron jobs ทั้งหมด
hermes cron list
```

---

### Example: Automated Log Monitor with Hermes

ตัวอย่างการตั้งค่า Hermes ให้ตรวจสอบ logs อัตโนมัติทุก 30 นาที:

```bash
# สร้าง cron job ให้ Hermes ตรวจสอบ logs ทุก 30 นาที
hermes cron create \
  --name "log-monitor" \
  --schedule "30m" \
  --prompt "อ่าน logs จาก /var/log/app.log (30 นาทีล่าสุด).
    ถ้าเจอ ERROR หรือ CRITICAL → สรุปปัญหาและแนะนำวิธีแก้
    ถ้าเจอ pattern เดิมซ้ำ ๆ → บอกว่าเป็น recurring issue พร้อมความถี่
    ถ้าปกติ → ไม่ต้องรายงานอะไร"
```

**Pipeline การทำงาน:**
1. Hermes cron trigger ทุก 30 นาที
2. อ่าน `/var/log/app.log` เฉพาะบรรทัดใหม่ในช่วง 30 นาที
3. Qwen 3.6 27B (local) วิเคราะห์ log patterns
4. ถ้าพบปัญหา → สร้าง report + ส่ง notification
5. บันทึกผลลง `/var/log/hermes-monitor.log`

---

### ข้อดีของ Hermes + Local LLM

| ข้อดี | รายละเอียด |
|-------|-----------|
| 🔒 **Privacy 100%** | ทุกอย่างรันบนเครื่อง — โค้ด/log/secret ไม่หลุดออกนอกเครื่อง |
| 💰 **ฟรี ไม่มีค่า API** | ใช้ local LLM ผ่าน Ollama — ไม่ต้องจ่ายต่อ token |
| ⚡ **Latency ต่ำ** | ตอบกลับในวินาที — ไม่ต้องรอ network round-trip |
| 🛠️ **Extensible** | เพิ่ม custom tools/plugins ได้เองตาม workflow |
| 🔌 **Offline ได้** | ทำงานได้แม้ไม่มีอินเทอร์เน็ต — เหมาะกับ air-gapped environments |
| 🎯 **Customizable** | ปรับแต่ง prompt, tools, schedule ให้ตรงกับทีม |

---

### เปรียบเทียบ: Hermes + Cloud LLM vs Local LLM

| | **Cloud LLM** (DeepSeek V4, Claude Opus) | **Local LLM** (Qwen 3.6, DeepSeek R1) |
|---|---|---|
| 🔒 Privacy | ❌ ส่งข้อมูลออกนอกเครื่อง | ✅ 100% local — ปลอดภัยสูงสุด |
| 💰 Cost | ❌ จ่ายต่อ token ($3-15/1M tokens) | ✅ ฟรี — ใช้ทรัพยากรเครื่องตัวเอง |
| ⚡ Speed | ⚡⚡⚡ เร็ว (data center GPU) | ⚡⚡ เร็วพอใช้ (M5 ~20 tok/s) |
| 🧠 Quality | 🏆 ดีที่สุดสำหรับงานซับซ้อนมาก | ดีมาก — เพียงพอสำหรับงาน DevOps ส่วนใหญ่ |
| 📡 Offline | ❌ ต้องต่อเน็ต | ✅ ใช้ได้ทุกที่ |
| 📏 Rate Limit | ❌ มี limit ต่อนาที/วัน | ✅ ไม่มี limit |
| 🎛️ Control | ❌ ขึ้นอยู่กับผู้ให้บริการ | ✅ ควบคุมได้ 100% |

> 💡 **แนะนำ:** ใช้ Local LLM (Qwen 3.6 27B) เป็น default สำหรับงานทั่วไป (review, logs, tests) — และ switch เป็น Cloud LLM เฉพาะงานที่ต้องการ reasoning สูงมาก ๆ เช่น deep algorithmic analysis

---

## ⚡ MLX — Apple Native Acceleration

MLX เป็น framework ของ Apple สำหรับรัน ML บน Apple Silicon — เร็วกว่า Ollama บางเคส

```bash
# ติดตั้ง MLX
pip install mlx mlx-lm

# รันโมเดลผ่าน MLX (เร็วกว่า Ollama สำหรับบางโมเดล)
mlx_lm.generate --model mlx-community/Qwen3.6-27B-4bit \
  --prompt "เขียนฟังก์ชัน Python สำหรับ validate Kubernetes YAML"

# รันเป็น server แบบ OpenAI API
mlx_lm.server --model mlx-community/Qwen3.6-27B-4bit
```

> 💡 **เมื่อไหร่ควรใช้ MLX:** สำหรับโมเดลที่ใช้บ่อยมาก ๆ และต้องการความเร็วสูงสุด  
> **เมื่อไหร่ควรใช้ Ollama:** สำหรับ general use — ง่ายกว่า, มี ecosystem ใหญ่กว่า

---

## 📊 Performance Benchmarks (M5, 32GB)

| โมเดล | Quant | Tokens/s | RAM | การใช้งาน |
|-------|-------|----------|-----|----------|
| Gemma 4 12B | Q4_K_M | ~38 tok/s | ~8 GB | ทั่วไป, เร็ว |
| Qwen 3.6 27B | Q4_K_M | ~20 tok/s | ~16 GB | 🏆 **แนะนำ** — coding + DevOps |
| Mistral Small 3.2 24B | Q4_K_M | ~18 tok/s | ~15 GB | autocomplete, instruction |
| Granite 4.1 30B | Q4_K_M | ~13 tok/s | ~18 GB | reasoning, code review |
| DeepSeek R1 32B | Q4_K_M | ~14 tok/s | ~20 GB | deep reasoning, algorithms |
| Nemotron 3 33B | Q4_K_M | ~12 tok/s | ~20 GB | multimodal, analysis ลึก |

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

# 2. ดาวน์โหลดโมเดลหลัก (เลือกเพียง 1-2 ตัว)
ollama pull qwen3.6:27b
ollama pull gemma4:12b

# 3. ทดสอบ
ollama run qwen3.6:27b "เขียน Dockerfile สำหรับ React app ที่ใช้ nginx"
```
