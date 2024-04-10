import time
import speech_recognition as sr
from googletrans import Translator
from gtts import gTTS
from pydub import AudioSegment
from pydub.playback import play

r = sr.Recognizer()
translator = Translator()


def text_to_speech(text, lang='en'):
    tts = gTTS(text=text, lang=lang, slow=False)
    return tts


while True:
    with sr.Microphone() as source:
        print("Speak Now!")
        audio = r.listen(source)
        try:
            speech_text = r.recognize_google(audio)
            print(speech_text)
            if speech_text.lower() == "exit":
                break
        except sr.UnknownValueError:
            print("Could Not Understand")
        except sr.RequestError:
            print("Could Not request results from Google")

        translated_text = translator.translate(speech_text, dest='de')
        print(translated_text.text)

        tts = text_to_speech(translated_text.text, lang='de')
        tts.save("translated_audio.mp3")

        audio = AudioSegment.from_mp3("translated_audio.mp3")
        play(audio)

    # Add a delay to release resources
    time.sleep(1)
