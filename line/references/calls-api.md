# Calls API Integration Guide

Connect web apps, mobile apps, or custom telephony stacks to your Line agent via the Calls API WebSocket protocol.

## Use Cases

- **Web Applications**: Browser-based voice interfaces using WebRTC/getUserMedia
- **Mobile Apps**: Native iOS/Android apps with audio capture
- **Custom Telephony**: BYO SIP/WebRTC infrastructure, contact center integration

## Authentication

### 1. Get an Access Token

Request a short-lived token from your backend:

```bash
curl -X POST https://api.cartesia.ai/agents/access-token \
  -H "X-API-Key: YOUR_CARTESIA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"agent_id": "your-agent-id"}'
```

Response:
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "expires_in": 300
}
```

### 2. Connect via WebSocket

```javascript
const ws = new WebSocket(
  `wss://api.cartesia.ai/agents/stream/${agentId}`,
  {
    headers: {
      "Authorization": `Bearer ${accessToken}`,
      "Cartesia-Version": "2025-04-16"
    }
  }
);
```

## WebSocket Protocol

### Connection Flow

```
Client                              Server
  │                                    │
  │──── WebSocket Connect ────────────▶│
  │                                    │
  │──── start event ──────────────────▶│
  │                                    │
  │◀─── ack event ────────────────────│
  │                                    │
  │──── media_input (audio) ──────────▶│
  │                                    │
  │◀─── media_output (audio) ─────────│
  │                                    │
  │◀─── clear (on interruption) ──────│
  │                                    │
```

### Event Types

#### `start` - Initiate the Call

Send immediately after connection to configure the session:

```json
{
  "event": "start",
  "call_id": "unique-call-id",
  "input_format": "pcm_16000",
  "output_format": "pcm_16000",
  "config": {
    "voice_id": "your-voice-id"
  },
  "agent": {
    "system_prompt": "You are a helpful assistant.",
    "introduction": "Hello! How can I help you today?"
  },
  "metadata": {
    "user_id": "user-123",
    "session_id": "session-456"
  }
}
```

#### `media_input` - Send Audio to Agent

Stream user audio to the agent:

```json
{
  "event": "media_input",
  "data": "base64-encoded-audio-data"
}
```

#### `media_output` - Receive Agent Audio

Agent responses come as audio chunks:

```json
{
  "event": "media_output",
  "data": "base64-encoded-audio-data"
}
```

#### `ack` - Server Acknowledgment

Confirmation that the server received and processed an event:

```json
{
  "event": "ack",
  "ref": "start"
}
```

#### `clear` - Interruption Signal

Sent when the user interrupts the agent (barge-in). Stop playing current audio:

```json
{
  "event": "clear"
}
```

#### `dtmf` - DTMF Tones

Send or receive touch-tone signals:

```json
{
  "event": "dtmf",
  "button": "5"
}
```

Valid buttons: `"0"` - `"9"`, `"*"`, `"#"`

## Audio Formats

| Format | Sample Rate | Encoding | Use Case |
|--------|-------------|----------|----------|
| `mulaw_8000` | 8kHz | mu-law | Telephony (lowest bandwidth) |
| `pcm_16000` | 16kHz | 16-bit PCM | Standard quality |
| `pcm_24000` | 24kHz | 16-bit PCM | High quality |
| `pcm_44100` | 44.1kHz | 16-bit PCM | Studio quality |

Choose based on your bandwidth and quality requirements. `pcm_16000` is recommended for most web/mobile applications.

## JavaScript Example

Complete browser integration:

```javascript
class CartesiaVoiceClient {
  constructor(agentId, accessToken) {
    this.agentId = agentId;
    this.accessToken = accessToken;
    this.ws = null;
    this.audioContext = null;
    this.mediaStream = null;
  }

  async connect() {
    // Connect to Cartesia
    this.ws = new WebSocket(
      `wss://api.cartesia.ai/agents/stream/${this.agentId}`
    );

    this.ws.onopen = () => {
      // Send start event with auth
      this.ws.send(JSON.stringify({
        event: "start",
        call_id: crypto.randomUUID(),
        access_token: this.accessToken,
        input_format: "pcm_16000",
        output_format: "pcm_16000",
        agent: {
          system_prompt: "You are a helpful assistant.",
          introduction: "Hello! How can I help?"
        }
      }));
    };

    this.ws.onmessage = (event) => {
      const message = JSON.parse(event.data);

      switch (message.event) {
        case "media_output":
          this.playAudio(message.data);
          break;
        case "clear":
          this.stopPlayback();
          break;
        case "ack":
          console.log("Server acknowledged:", message.ref);
          break;
      }
    };

    // Start capturing microphone
    await this.startMicrophone();
  }

  async startMicrophone() {
    this.mediaStream = await navigator.mediaDevices.getUserMedia({ audio: true });
    this.audioContext = new AudioContext({ sampleRate: 16000 });

    const source = this.audioContext.createMediaStreamSource(this.mediaStream);
    const processor = this.audioContext.createScriptProcessor(4096, 1, 1);

    processor.onaudioprocess = (e) => {
      if (this.ws?.readyState === WebSocket.OPEN) {
        const pcmData = e.inputBuffer.getChannelData(0);
        const int16Data = this.floatTo16BitPCM(pcmData);
        const base64 = btoa(String.fromCharCode(...new Uint8Array(int16Data.buffer)));

        this.ws.send(JSON.stringify({
          event: "media_input",
          data: base64
        }));
      }
    };

    source.connect(processor);
    processor.connect(this.audioContext.destination);
  }

  floatTo16BitPCM(float32Array) {
    const int16Array = new Int16Array(float32Array.length);
    for (let i = 0; i < float32Array.length; i++) {
      const s = Math.max(-1, Math.min(1, float32Array[i]));
      int16Array[i] = s < 0 ? s * 0x8000 : s * 0x7FFF;
    }
    return int16Array;
  }

  playAudio(base64Data) {
    // Decode and play audio
    const binaryString = atob(base64Data);
    const bytes = new Uint8Array(binaryString.length);
    for (let i = 0; i < binaryString.length; i++) {
      bytes[i] = binaryString.charCodeAt(i);
    }
    // Queue audio for playback...
  }

  stopPlayback() {
    // Stop current audio playback on interruption
  }

  disconnect() {
    this.mediaStream?.getTracks().forEach(track => track.stop());
    this.ws?.close();
    this.audioContext?.close();
  }
}

// Usage
const client = new CartesiaVoiceClient("agent-id", "access-token");
await client.connect();
```

## Connection Management

### Keepalive

Send periodic pings to maintain the connection:

```javascript
setInterval(() => {
  if (ws.readyState === WebSocket.OPEN) {
    ws.send(JSON.stringify({ event: "ping" }));
  }
}, 30000);
```

### Timeouts

- **Connection timeout**: 30 seconds to establish WebSocket
- **Idle timeout**: 60 seconds of no audio before server closes connection
- **Max call duration**: Configured per agent (default: 30 minutes)

### Error Handling

```javascript
ws.onerror = (error) => {
  console.error("WebSocket error:", error);
};

ws.onclose = (event) => {
  if (event.code === 1000) {
    console.log("Call ended normally");
  } else if (event.code === 4001) {
    console.error("Authentication failed");
  } else if (event.code === 4002) {
    console.error("Agent not found");
  } else {
    console.error("Connection closed:", event.code, event.reason);
  }
};
```

### Common Close Codes

| Code | Meaning |
|------|---------|
| 1000 | Normal closure |
| 4001 | Authentication failed |
| 4002 | Agent not found |
| 4003 | Rate limited |
| 4004 | Invalid message format |

## Mobile Integration

### iOS (Swift)

Use `URLSessionWebSocketTask` for WebSocket connections and `AVAudioEngine` for audio capture/playback.

### Android (Kotlin)

Use OkHttp WebSocket client and `AudioRecord`/`AudioTrack` for audio handling.

### React Native

Use `react-native-websocket` and `expo-av` or `react-native-audio-api` for cross-platform audio.

## Best Practices

1. **Buffer audio** - Queue outgoing audio and send in chunks (e.g., 100ms intervals)
2. **Handle interruptions** - Stop playback immediately on `clear` events
3. **Graceful reconnection** - Implement exponential backoff for reconnects
4. **Echo cancellation** - Use platform AEC to prevent feedback loops
5. **Visual feedback** - Show speaking/listening states to users
