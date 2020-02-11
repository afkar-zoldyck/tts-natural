# Install TTS
pip install --upgrade google-cloud-texttospeech

# Upload file to google (sometimes you have to try it several times)
from google.colab import files
uploaded = files.upload()

# Create credential object and test access 
from google.oauth2 import service_account
from google.cloud import texttospeech
credentials = service_account.Credentials.from_service_account_file('tts.json') # here insert the name of the json file

texttospeech.TextToSpeechClient(credentials=credentials)

# Load text file
name="speech.txt" # the name of the uploaded txt file that you want to have spoken
output="output.mp3"
with open(name, 'r') as file:
    data = file.read().replace('\n', ' ')

# Convert text to speech

# Instantiates a client
client = texttospeech.TextToSpeechClient(credentials=credentials)

# Set the text input to be synthesized
synthesis_input = texttospeech.types.SynthesisInput(text=data)

# Build the voice request
voice = texttospeech.types.VoiceSelectionParams(
    name="id-ID-Wavenet-D",  
    language_code='id-ID',
    ssml_gender=texttospeech.enums.SsmlVoiceGender.NEUTRAL
 )

# Select the type of audio file
audio_config = texttospeech.types.AudioConfig(
    audio_encoding=texttospeech.enums.AudioEncoding.MP3,
    speaking_rate=0.8
    )

# Perform the text-to-speech request on the text input with the selected
# voice parameters and audio file type
response = client.synthesize_speech(synthesis_input, voice, audio_config)

# The response's audio_content is binary.
with open(output, 'wb') as out:
    # Write the response to the output file.
    out.write(response.audio_content)
    print('Audio content written "output.mp3"')
