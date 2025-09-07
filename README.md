import sounddevice as sd
import numpy as np
import scipy.io.wavfile as wav
import speech_recognition as sr
from googletrans import Translator
import random


words_by_level = {
    "easy": ["кот", "собака", "яблоко", "молоко", "солнце"],
    "medium": ["банан", "школа", "друг", "окно", "жёлтый"],
    "hard": ["технология", "университет", "информация", "произношение", "воображение"]
}

duration = 5  # секунды записи
sample_rate = 44100
recognizer = sr.Recognizer()
translator = Translator()

while True:
        
        print("Welcome to the master of translation game!")
        print("You have 3 lives, be careful!")

        level = input("what level do you want to try: easy, medium or hard ")

        if level == "easy":
            word = random.choice(words_by_level["easy"])
            print(word)

        if level == "medium":
            word = random.choice(words_by_level["medium"])
            print(word)

        if level == "hard":
            word = random.choice(words_by_level["hard"])
            print(word)



        print("🎙Translate the word...")
        recording = sd.rec(
        int(duration * sample_rate), # длительность записи в сэмплах
        samplerate=sample_rate,      # частота дискретизации
        channels=1,                  # 1 — это моно
        dtype="int16")               # формат аудиоданных
        sd.wait()  # ждём завершения записи


        recognizer = sr.Recognizer()
        with sr.AudioFile("output.wav") as source:
            audio = recognizer.record(source)

        try:
            text = recognizer.recognize_google(audio, language="en-EN")
            print("📝You said:", text)
            translation = translator.translate(word, src="ru", dest="en").text.lower()
            if text == translation:
                 print("Correct")
            else:
                 print("Incorrect")
            


        except sr.UnknownValueError:             # - если Google не понял речь (шум, молчание)
            print("Не удалось распознать речь.")
        except sr.RequestError as e:             # - если нет интернета или API недоступен
            print(f"Ошибка сервиса: {e}")
