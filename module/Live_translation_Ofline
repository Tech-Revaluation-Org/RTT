import pyaudio
import queue
import threading
import speech_recognition as sr
from translate import Translator

# Function to transcribe audio data into text using PocketSphinx for offline recognition
def transcribe_audio(audio_data):
    recognizer = sr.Recognizer()
    try:
        text = recognizer.recognize_sphinx(audio_data)
        print("Transcription:", text)
        return text
    except sr.UnknownValueError:
        print("Offline Speech Recognition could not understand audio")
    except sr.RequestError as e:
        print("Could not perform offline recognition; {0}".format(e))
    return None

# Function to translate text into the target language
def translate_text(text, target_language):
    translator = Translator(to_lang=target_language)
    translation = translator.translate(text)
    print("Translation:", translation)
    return translation

# Function to capture system audio in real-time
def capture_audio(q):
    CHUNK = 1024
    FORMAT = pyaudio.paInt16
    CHANNELS = 1
    RATE = 16000
    audio = pyaudio.PyAudio()
    stream = audio.open(format=FORMAT, channels=CHANNELS,
                        rate=RATE, input=True,
                        frames_per_buffer=CHUNK)
    print("* Listening...")
    while True:
        audio_data = stream.read(CHUNK)
        q.put(audio_data)

# Function to process captured audio data
def process_audio(q, target_language):
    while True:
        audio_data = q.get()
        transcription = transcribe_audio(audio_data)
        if transcription:
            translate_text(transcription, target_language)

# Create a queue to communicate between audio capture and processing threads
audio_queue = queue.Queue()

# Specify target language for translation
target_language = 'fr'  # Change to your desired target language code

# Start audio capture and processing threads
capture_thread = threading.Thread(target=capture_audio, args=(audio_queue,))
process_thread = threading.Thread(target=process_audio, args=(audio_queue, target_language))
capture_thread.start()
process_thread.start()

  # Wait for threads to finish (press Ctrl+C for stop)
try:
    capture_thread.join()
    process_thread.join()
except KeyboardInterrupt:
    print("Stopping...")
