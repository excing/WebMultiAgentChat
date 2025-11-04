# Microsoft TTS API 文档

## 概述

这是一个基于 Microsoft Azure 语音服务的文本转语音 (TTS) API，提供了多个接口来满足不同的使用场景。

**基础 URL**: `https://your-domain.com`

---

## 认证

所有 API 请求都需要提供有效的 API 密钥进行认证。

### 认证方式

**方式 1**: URL 参数（推荐用于 TTS 接口）
```
?api_key=YOUR_API_KEY
```

**方式 2**: Authorization Header（推荐用于 OpenAI 兼容接口）
```
Authorization: Bearer YOUR_API_KEY
```

---

## API 接口

### 1. 文本转语音 (TTS)

将文本转换为语音音频文件。

#### 请求

```http
GET /tts?api_key={api_key}&t={text}&v={voice}&r={rate}&p={pitch}&s={style}&o={format}&d={download}
```

#### 参数说明

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `api_key` | string | 是 | - | API 密钥 |
| `t` | string | 是 | - | 要转换的文本（需 URL 编码） |
| `v` | string | 否 | zh-CN-XiaoxiaoMultilingualNeural | 语音名称 |
| `r` | number | 否 | 0 | 语速调整 (-100 到 100) |
| `p` | number | 否 | 0 | 音调调整 (-100 到 100) |
| `s` | string | 否 | general | 语音风格 |
| `o` | string | 否 | audio-24khz-48kbitrate-mono-mp3 | 输出格式 |
| `d` | boolean | 否 | false | 是否下载文件 |

#### 语音风格选项

- `general` - 标准
- `cheerful` - 愉快
- `sad` - 悲伤
- `angry` - 愤怒
- `fearful` - 恐惧
- `friendly` - 友好
- `calm` - 平静
- `assistant` - 助理
- `chat` - 随意
- `newscast` - 新闻播报
- `customerservice` - 客服
- 更多风格请参考语音列表接口

#### 输出格式选项

- `audio-24khz-48kbitrate-mono-mp3` (默认)
- `audio-48khz-192kbitrate-mono-opus`
- `audio-16khz-32kbitrate-mono-mp3`
- 其他 Azure TTS 支持的格式

#### 示例

**请求**:
```bash
curl "https://your-domain.com/tts?api_key=YOUR_KEY&t=Hello%20World&v=en-US-AriaNeural&r=10&p=5&s=cheerful"
```

**响应**:
- 成功: 返回音频文件流 (audio/mpeg)
- 失败 (401): 
```json
{
  "error": "Unauthorized",
  "message": "无效的 API 密钥，请确保您提供了正确的密钥。",
  "status": 401
}
```

---

### 2. OpenAI 兼容接口

提供与 OpenAI Text-to-Speech API 兼容的接口。

#### 请求

```http
POST /v1/audio/speech
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY
```

#### 请求体

```json
{
  "model": "tts-1",
  "input": "要转换的文本",
  "voice": "alloy",
  "speed": 1.0,
  "response_format": "mp3"
}
```

#### 参数说明

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `model` | string | 是 | - | 模型名称 (如: tts-1, tts-1-hd) |
| `input` | string | 是 | - | 要转换的文本 |
| `voice` | string | 否 | alloy | 语音类型 |
| `speed` | number | 否 | 1.0 | 语速 (0.25-4.0) |
| `response_format` | string | 否 | mp3 | 输出格式 (mp3/opus) |

#### 语音映射

| OpenAI Voice | 映射到 Azure 语音 |
|--------------|-------------------|
| `alloy` | zh-CN-XiaoxiaoMultilingualNeural |
| `echo` | zh-CN-YunxiNeural |
| `fable` | zh-CN-XiaomoNeural |
| `onyx` | zh-CN-YunjianNeural |
| `nova` | zh-CN-XiaochenNeural |
| `shimmer` | en-US-AriaNeural |

#### 示例

**请求**:
```bash
curl https://your-domain.com/v1/audio/speech \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "tts-1",
    "input": "Hello, how are you today?",
    "voice": "alloy",
    "speed": 1.2
  }' \
  --output speech.mp3
```

**响应**:
- 成功: 返回音频文件流
- 失败: 返回 JSON 错误信息

---

### 3. 获取语音列表

获取所有可用的语音选项。

#### 请求

```http
GET /voices?l={locale}&f={filter}
```

#### 参数说明

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `l` | string | 否 | 按语言代码筛选 (如: zh, en, ja) |
| `f` | string | 否 | 其他筛选条件 |

#### 示例

**请求**:
```bash
# 获取所有语音
curl "https://your-domain.com/voices"

# 获取中文语音
curl "https://your-domain.com/voices?l=zh"

# 获取英文语音
curl "https://your-domain.com/voices?l=en"
```

**响应**:
```json
[
  {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoMultilingualNeural)",
    "DisplayName": "Xiaoxiao Multilingual",
    "LocalName": "晓晓多语言",
    "ShortName": "zh-CN-XiaoxiaoMultilingualNeural",
    "Gender": "Female",
    "Locale": "zh-CN",
    "LocaleName": "Chinese (Mandarin, Simplified)",
    "StyleList": ["general", "cheerful", "sad", "angry", "fearful"],
    "SampleRateHertz": "48000",
    "VoiceType": "Neural",
    "Status": "GA"
  }
]
```

---

### 4. 阅读+ (Reader) 配置接口

生成用于阅读+应用的配置链接。

#### 请求

```http
GET /reader.json?api_key={api_key}&v={voice}&r={rate}&p={pitch}&s={style}&n={name}
```

#### 参数说明

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `api_key` | string | 是 | - | API 密钥 |
| `v` | string | 否 | - | 语音名称 |
| `r` | number | 否 | - | 语速 |
| `p` | number | 否 | - | 音调 |
| `s` | string | 否 | - | 风格 |
| `n` | string | 否 | Microsoft TTS | 显示名称 |

#### 示例

**请求**:
```bash
curl "https://your-domain.com/reader.json?api_key=YOUR_KEY&v=zh-CN-XiaoxiaoNeural&r=10&n=我的TTS"
```

**响应**:
```json
{
  "id": 1699999999999,
  "name": "我的TTS",
  "url": "https://your-domain.com/tts?t={{java.encodeURI(speakText)}}&r={{speakSpeed*4}}&v=zh-CN-XiaoxiaoNeural&api_key=YOUR_KEY"
}
```

---

### 5. 爱阅记 (IFreeTime) 配置接口

生成用于爱阅记应用的配置。

#### 请求

```http
GET /ifreetime.json?api_key={api_key}&v={voice}&r={rate}&p={pitch}&s={style}&n={name}
```

#### 参数说明

与 Reader 接口相同。

#### 示例

**请求**:
```bash
curl "https://your-domain.com/ifreetime.json?api_key=YOUR_KEY&v=zh-CN-XiaoxiaoNeural"
```

**响应**:
```json
{
  "loginUrl": "",
  "maxWordCount": "",
  "customRules": {},
  "ttsConfigGroup": "Azure",
  "_TTSName": "Microsoft TTS",
  "_ClassName": "JxdAdvCustomTTS",
  "_TTSConfigID": "550e8400-e29b-41d4-a716-446655440000",
  "httpConfigs": {
    "useCookies": 1,
    "headers": {}
  },
  "voiceList": [],
  "ttsHandles": [...]
}
```

---

## SSML 支持

API 支持在文本中使用 SSML 标签进行高级控制。

### 支持的 SSML 标签

- `<break>` - 插入停顿
- `<prosody>` - 控制语速、音调、音量
- `<emphasis>` - 强调
- `<voice>` - 改变语音
- `<say-as>` - 指定文本解释方式
- `<phoneme>` - 音素
- `<audio>` - 插入音频
- `<p>` / `<s>` - 段落和句子
- `<sub>` - 替换
- `<mstts:*>` - Microsoft 特定标签

### 示例

```xml
<speak>
  <voice name="zh-CN-XiaoxiaoNeural">
    <prosody rate="10%" pitch="5%">
      你好，<break time="500ms"/>欢迎使用语音服务！
    </prosody>
  </voice>
</speak>
```

---

## 错误处理

### 错误响应格式

```json
{
  "error": "错误类型",
  "message": "详细错误信息",
  "status": 错误状态码
}
```

### 常见错误码

| 状态码 | 说明 |
|--------|------|
| 400 | 请求参数错误 |
| 401 | 认证失败，API 密钥无效 |
| 405 | 请求方法不允许 |
| 500 | 服务器内部错误 |

---

## 使用限制

- 文本长度建议不超过 5000 字符
- 语速范围: -100 到 100
- 音调范围: -100 到 100
- 速度范围 (OpenAI 接口): 0.25 到 4.0
- API 调用需要有效的认证

---

## 常见语音列表

### 中文语音

| 语音名称 | 性别 | 短名称 |
|----------|------|--------|
| 晓晓多语言 | 女 | zh-CN-XiaoxiaoMultilingualNeural |
| 晓晓 | 女 | zh-CN-XiaoxiaoNeural |
| 云希 | 男 | zh-CN-YunxiNeural |
| 晓墨 | 女 | zh-CN-XiaomoNeural |
| 云健 | 男 | zh-CN-YunjianNeural |
| 晓陈 | 儿童 | zh-CN-XiaochenNeural |

### 英文语音

| 语音名称 | 性别 | 短名称 |
|----------|------|--------|
| Aria | 女 | en-US-AriaNeural |
| Guy | 男 | en-US-GuyNeural |
| Jenny | 女 | en-US-JennyNeural |

---

## 集成示例

### JavaScript

```javascript
// 基础 TTS
const text = encodeURIComponent("你好世界");
const audioUrl = `https://your-domain.com/tts?api_key=YOUR_KEY&t=${text}&v=zh-CN-XiaoxiaoNeural`;

const audio = new Audio(audioUrl);
audio.play();

// OpenAI 兼容接口
const response = await fetch('https://your-domain.com/v1/audio/speech', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    model: 'tts-1',
    input: '你好世界',
    voice: 'alloy'
  })
});

const audioBlob = await response.blob();
const audioUrl = URL.createObjectURL(audioBlob);
```

### Python

```python
import requests

# 基础 TTS
url = "https://your-domain.com/tts"
params = {
    "api_key": "YOUR_KEY",
    "t": "你好世界",
    "v": "zh-CN-XiaoxiaoNeural",
    "r": "10",
    "p": "5"
}

response = requests.get(url, params=params)
with open("output.mp3", "wb") as f:
    f.write(response.content)

# OpenAI 兼容接口
url = "https://your-domain.com/v1/audio/speech"
headers = {
    "Authorization": "Bearer YOUR_KEY",
    "Content-Type": "application/json"
}
data = {
    "model": "tts-1",
    "input": "你好世界",
    "voice": "alloy"
}

response = requests.post(url, headers=headers, json=data)
with open("output.mp3", "wb") as f:
    f.write(response.content)
```

### cURL

```bash
# 基础 TTS
curl "https://your-domain.com/tts?api_key=YOUR_KEY&t=Hello&v=en-US-AriaNeural" \
  --output speech.mp3

# OpenAI 兼容接口
curl https://your-domain.com/v1/audio/speech \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"tts-1","input":"Hello","voice":"alloy"}' \
  --output speech.mp3
```

---

## 技术支持

如有问题，请访问: [GitHub Repository](https://github.com/zuoban/tts)

---

**最后更新**: 2024年11月