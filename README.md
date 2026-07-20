# 🤖 Local LLM on Mac M5 — คู่มือสำหรับ DevOps/SRE

คู่มือฉบับสมบูรณ์สำหรับการรัน AI/LLM บน MacBook Air M5 (32GB RAM) สำหรับงาน DevOps และ SRE เนื้อหาทั้งหมดเป็นภาษาไทย

## 📖 อ่านคู่มือ

ดูคู่มือเต็มได้ที่ **[GitHub Pages](https://wsangsrichan.github.io/mac-local-llm-guide/)**

## 📋 เนื้อหา

- 🧠 ทำไมต้องรัน LLM บนเครื่อง
- 💻 M5 + 32GB RAM — ทำอะไรได้บ้าง
- 📦 ติดตั้ง Ollama
- 🎯 โมเดลแนะนำแยกตาม Use Case (Coding, Code Review, Security, K8s, Log Analysis)
- 🔧 Integration กับ Tools (VS Code, CLI, Open WebUI)
- 📋 Use Case Examples
- ⚡ MLX — Apple Native Acceleration
- 📊 Performance Benchmarks
- 🔐 Security Tips

## 🚀 Quick Start

```bash
brew install ollama
ollama pull qwen2.5-coder:14b
ollama pull qwen2.5:14b
ollama run qwen2.5-coder:14b "เขียน Dockerfile สำหรับ React app ที่ใช้ nginx"
```
