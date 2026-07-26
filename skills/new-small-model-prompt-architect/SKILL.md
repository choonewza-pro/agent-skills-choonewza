---
name: new-small-model-prompt-architect
description: >-
  วิเคราะห์ ปรับแต่ง หรือสร้าง System Prompt สำหรับโมเดลขนาดเล็กที่รันบน Ollama เป็นหลัก
  (Gemma, Qwen, Llama, Phi, Mistral, DeepSeek และรุ่นเล็กอื่นๆ) ซึ่งต้องการโครงสร้าง prompt
  ที่ explicit และเข้าใจกลไกเฉพาะของ Ollama (Modelfile, พารามิเตอร์ think, num_ctx, tool
  support ต่อ tag) มากกว่าโมเดล frontier ขนาดใหญ่ ใช้ skill นี้ทันทีเมื่อผู้ใช้พูดถึง
  ollama run/create/show, Modelfile, การ deploy โมเดลขนาดเล็กบน Ollama หรือระบุชื่อโมเดล
  รุ่นเล็กที่ตั้งใจรันบน Ollama
---

# Small / Local Model Prompt Architect

Skill นี้ช่วยวิเคราะห์ ปรับปรุง หรือออกแบบ system prompt สำหรับโมเดลขนาดเล็ก (≤12B) ที่ตั้งใจ
รันบน **Ollama เป็นหลัก** ไม่ว่าจะเป็นตระกูลไหน (Gemma, Qwen, Llama, Phi, Mistral, DeepSeek ฯลฯ)
— โมเดลกลุ่มนี้มี judgment จำกัดกว่าโมเดล frontier ขนาดใหญ่มาก การเขียน prompt ที่พึ่งการตีความ
nuance หรือปล่อยให้โมเดลตัดสินใจเองจะทำให้ทำงานไม่เสถียรหรือหลุด format ได้ง่าย ต้องเขียนแบบ
explicit และให้โครงสร้างที่ชัดเจนแทน หลักการทั่วไปในไฟล์นี้ (ตารางเปรียบเทียบ, XML delimiter)
ใช้ได้กับทุกตระกูล ส่วนกลไกควบคุม thinking/parameter ให้ยึดตาม**พารามิเตอร์ที่ Ollama เอง
เปิดให้ใช้งาน** (ดูหัวข้อ "Control token เฉพาะรุ่น") มากกว่าการฝัง control token ดิบๆ ลงใน
prompt เอง เพราะ Ollama มีกลไกจัดการ thinking ของตัวเองที่ไม่ได้ทำงานเหมือนกับตอนรันโมเดลตรงๆ
เสมอไป

## เลือกโหมดการทำงานจากคำขอของผู้ใช้

ไม่มี parameter บังคับ — อ่านเจตนาจากข้อความของผู้ใช้โดยตรง:

- ผู้ใช้แปะ prompt เดิมที่ใช้กับโมเดลเล็กมาพร้อมถามจุดอ่อน หรือสงสัยว่าทำไมโมเดลตอบหลุด format/วนซ้ำ → โหมด **analyze**
- ผู้ใช้อยากให้ย่อ/จัดโครงสร้างใหม่ prompt ที่มีอยู่แล้วให้เหมาะกับโมเดลเป้าหมาย → โหมด **optimize**
- ผู้ใช้อธิบายเป้าหมายของ agent ที่ยังไม่มี prompt และระบุโมเดลเป้าหมาย → โหมด **generate**

ถ้าไม่รู้ว่าโมเดลเป้าหมายคือรุ่นไหน (Gemma 4 ขนาดไหน หรือ Qwen3.5 ขนาดไหน) ให้ถามก่อนเริ่มทำ
เพราะ control token และ context window ต่างกันตามรุ่น

## หลักคิดหลัก: ทำไมโมเดลกลุ่มนี้ต้องการ prompt ที่ explicit กว่า

ตารางด้านล่างเทียบให้เห็นภาพว่าทำไมแนวทางถึงต่างจากโมเดล frontier ขนาดใหญ่ — **คอลัมน์ขวาคือ
แนวทางที่ skill นี้ใช้จริง คอลัมน์ซ้ายเป็นข้อมูลประกอบเพื่อให้เห็น contrast เท่านั้น ไม่ต้องอ่าน
เอกสารอื่นเพิ่ม:**

| กับ frontier model (Claude 5 gen)          | กับโมเดลขนาดเล็ก/local                                                                                                                                           |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| อธิบาย "ทำไม" แล้วปล่อยให้ใช้ judgment     | เขียนกฎแบบ explicit ตรงไปตรงมา ลด ambiguity ให้เหลือน้อยที่สุด — โมเดลเล็กตีความ nuance ได้จำกัดกว่ามาก                                                          |
| ลด XML tags ลง ใช้ heading ธรรมดาก็พอ      | **คง XML/delimiter ไว้เป็นค่าเริ่มต้น** — โมเดลเล็กแยก instruction ออกจาก data ได้แม่นยำขึ้นชัดเจนเมื่อมีขอบเขตตายตัว                                            |
| ลดจำนวนตัวอย่างลง เน้นออกแบบ interface แทน | **ใช้ 2-3 ตัวอย่างที่หลากหลาย** ยังจำเป็น — เกิน 5 ตัวอย่างไม่ค่อยช่วยเพิ่มและกิน context โดยเปล่าประโยชน์                                                       |
| ไว้ใจ native function calling เต็มที่      | function calling ของโมเดลกลุ่มนี้ (แม้รองรับ native) เสถียรน้อยกว่า Claude — ควรใส่ตัวอย่าง JSON tool-call ที่ถูกต้อง 1 ตัวอย่างไว้ใน prompt เป็น safety net     |
| context window ใหญ่ = ใช้ได้เต็มที่        | ตัวเลข context window ที่ประกาศ (256K/128K) เป็นค่า nominal — long-context recall จริงของโมเดลขนาดนี้มักแย่กว่าตัวเลขมาก ควรทดสอบเชิงประจักษ์ ไม่เชื่อ spec ตรงๆ |

## Control token เฉพาะรุ่น (ตั้งผิด = prompt ไม่ทำงานตามที่ตั้งใจ)

**สิ่งสำคัญสำหรับ Ollama โดยเฉพาะ:** อย่าใช้วิธีฝัง control token ดิบๆ (เช่น `<|think|>`,
`/think`, `/no_think`) ลงใน system prompt หรือ Modelfile `SYSTEM` field เพื่อ "บังคับ" โหมด
thinking แบบถาวร — มีรายงานยืนยันว่าวิธีนี้ **ไม่น่าเชื่อถือ**:

- Gemma 4: ลบ `<|think|>` ออกจาก Modelfile แล้ว thinking ควรจะปิด แต่ในทางปฏิบัติยังคง
  thinking แบบสั้นลงอยู่ (known behavior ที่มีรายงานใน GitHub issues)
- Qwen3.5: Modelfile `PARAMETER` ไม่มี directive สำหรับ thinking โดยตรง การสร้างโมเดล variant
  ที่ฝัง `/no_think` ไว้ใน Modelfile ไม่ได้ผลตามที่ตั้งใจอย่างสม่ำเสมอ

**วิธีที่ถูกต้อง:** ใช้พารามิเตอร์ `think` ที่ Ollama เปิดให้ใช้งานเป็น first-class feature
(รองรับตั้งแต่ Ollama 0.24+ สำหรับโมเดลที่ Ollama รู้จักว่ารองรับ thinking):

- CLI: flag `--think` / `--think=false` ตอนเรียก `ollama run`, หรือคำสั่งในโหมด interactive
- API: ใส่ `"think": true` / `false` (หรือ string ระดับความเข้ม เช่น `"low"/"medium"/"high"`
  สำหรับโมเดลที่รองรับ) ใน request ของ `/api/chat` หรือ `/api/generate`
- ต้องการ**ซ่อน** reasoning trace จากผู้ใช้แต่ยังอยากได้ผลลัพธ์ที่ดีจากการคิด: ใช้ flag
  `--hidethinking` แทนการปิด thinking ไปเลย
- ถ้า runtime/ไลบรารีที่ใช้ยังไม่รองรับการส่ง `think` ตรงๆ (เช่นบาง wrapper) ใช้ `num_predict`
  จำกัดความยาว token รวมเป็นทางเลือกรองในการคุมความยาวของ thinking block โดยอ้อม

**Gemma 4 (12B / E4B):**

- ใน Ollama: คุม thinking ผ่านพารามิเตอร์ `think` ด้านบน ไม่ใช่การแก้ system prompt
- multi-turn: อย่า feed thought block ของเทิร์นก่อนกลับเข้า history เก็บเฉพาะคำตอบสุดท้ายที่ผู้ใช้เห็น
- E4B = "effective 4B" (สถาปัตยกรรมแบบ MatFormer + Per-Layer Embeddings) เน้น on-device, context window 128K ส่วน 12B ได้ 256K

**Qwen3.5 (Small series 0.8B/2B/4B/9B):**

- ใน Ollama: คุม thinking ผ่านพารามิเตอร์ `think` ด้านบนเท่านั้น — reasoning ปิดโดย default อยู่แล้วสำหรับทุกรุ่นใน small series
- ถ้าเปิด thinking ไว้ ควรรักษา context length อย่างน้อย 128K (`num_ctx`) ไม่งั้นความสามารถ thinking จะเพี้ยน

## Checklist เฉพาะการ deploy local

- **Ollama/llama.cpp มัก set context window เริ่มต้นแค่ ~2,048 token** — ถ้า prompt ยาวกว่านั้นโมเดลอาจเริ่มวนซ้ำ (looping) หรือทำงานพัง ต้องตั้ง context length เองให้ครอบคลุม prompt จริง (`num_ctx` ใน Ollama, `-c` ใน llama.cpp)
- **Sampling parameters ต้อง set เอง** — โมเดล local ไม่มีค่า default ที่ tune ไว้ดีเท่า API ของ Claude ให้ระบุ temperature/top-k/top-p/min-p ตามคำแนะนำของแต่ละรุ่นชัดเจน อย่าปล่อยให้ runtime ใช้ default เฉยๆ
- **Function calling ต้อง verify ว่า serving framework รองรับจริง** — เช่น vLLM ต้องเปิด flag อย่าง `--enable-auto-tool-choice` พร้อมระบุ `--tool-call-parser` ให้ตรงกับ family ของโมเดล การที่โมเดลรองรับ native tool calling ไม่ได้แปลว่า runtime จะ parse ออกมาถูกเสมอ

## Output ตามโหมด

- **analyze**: ระบุจุดที่ prompt พึ่ง "judgment" ของโมเดลมากเกินไปสำหรับขนาดที่เลือกใช้ พร้อม flag จุดที่ยังไม่ได้ตั้ง control token ให้ถูกต้องตามรุ่นเป้าหมาย
- **optimize / generate**: คืน prompt ที่ใส่ XML delimiter รอบ section หลัก + ตัวอย่าง 2-3 ตัวอย่าง + control token ที่ตรงกับโมเดลเป้าหมาย พร้อมหมายเหตุ runtime config ที่ต้องตั้งคู่กัน (context window, sampling, tool-call parser)

## ตัวอย่าง (target: gemma4:e4b, ต้องการเปิด thinking)

```
<|think|>
<identity>
คุณคือผู้ช่วยตอบคำถามจากเอกสารภายในองค์กร ตอบสั้น ตรงประเด็น
</identity>

<data>
{{เอกสารอ้างอิง}}
</data>

<instructions>
1. อ่าน <data> เท่านั้น ห้ามใช้ความรู้ภายนอก
2. ถ้าคำตอบไม่มีใน <data> ให้ตอบว่า "ไม่มีข้อมูลในเอกสารนี้"
3. ตอบเป็นภาษาเดียวกับคำถามของผู้ใช้
</instructions>

<example>
คำถาม: นโยบายลาป่วยกี่วันต่อปี?
คำตอบ: ตามเอกสาร พนักงานลาป่วยได้ 30 วันต่อปี
</example>
```

(สังเกตว่า XML delimiter, ตัวอย่าง 1 ชุด, และ `<|think|>` ยังอยู่ครบ — ตรงข้ามกับแนวทาง Claude 5
gen ที่จะตัดสิ่งเหล่านี้ออกเพื่อความกระชับ เพราะโมเดลขนาดนี้ต้องการโครงสร้างที่ชัดเจนกว่า)
