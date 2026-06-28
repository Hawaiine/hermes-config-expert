# ElevenLabs TTS/STT Setup Reference

ElevenLabs provides both TTS (Text-to-Speech) and STT (Speech-to-Text) in Hermes.

## API Key

```bash
# .env (under $HERMES_HOME)
ELEVENLABS_API_KEY=sk_...
```

## TTS Configuration

```yaml
tts:
  provider: elevenlabs          # switch from 'edge' to 'elevenlabs'
  elevenlabs:
    voice_id: pNInz6obpgDQGcFmaJgB
    model_id: eleven_multilingual_v2
```

Supported voices and models are available via ElevenLabs API.

## STT Configuration

```yaml
stt:
  elevenlabs:
    model_id: scribe_v2
    language_code: ''            # auto-detect
    tag_audio_events: false
    diarize: false
```

## Switching the Active TTS Provider

```bash
# Via CLI
hermes config set tts.provider elevenlabs

# Via config.yaml edit
sed -i 's/^  provider: edge$/  provider: elevenlabs/' config.yaml
```

Verify with `/voice on` (voice-to-voice) or request TTS output in a session.

## Sourcing the Key in Test Scripts

Read from `.env` at runtime (avoid putting the key in test scripts):

```bash
export $(grep '^ELEVENLABS_API_KEY=*** /opt/data/.env | xargs)
```

Or from Python:

```python
import os
from dotenv import load_dotenv
load_dotenv('/opt/data/.env')
api_key = os.environ['ELEVENLABS_API_KEY']
```
