# MAX-1.0

from ast import Str
from sre_constants import BIGCHARSET
import wolframalpha
import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import pyjokes
import time
import requests
import json
from urllib.request import urlopen
from bs4 import BeautifulSoup
from cgitb import text

engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)

def speak(audio):
    print(audio)
    engine.say(audio)
    engine.runAndWait()

def wishMe():
    hour = int(datetime.datetime.now().hour)
    if hour >= 5 and hour < 12:
        speak("Good Morning!")
    elif hour >= 12 and hour < 18:
        speak("Good Afternoon!")
    else:
        speak("Good Evening!")
    assname = ("MAX 1.0 at your service")
    speak("I am your Assistant")
    speak(assname)

# def username():
#     speak("What should I call you?")
#     uname = takeCommand()
#     while uname == "" or uname==None:
#      uname = takeCommand()
#     speak("Welcome! If I got your name right,  " + uname + "  isn't it?")
#     speak("How can I help you?")

def takeCommand():
    query = None
    r = sr.Recognizer()
    r.energy_threshold = 600
    audio = ''
    with sr.Microphone() as source:
        print("Listening...")
        r.pause_threshold = 1
        audio = r.listen(source)
    try:
        print("Recognizing...")
        query = r.recognize_google(audio)
        print(f"User said: {query}\n")
    except Exception as e:
        # print(e)
        print("Unable to Recognize your voice.")
    return query

def getWeather(city):
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
    city = city.replace(" ", "+")
    res = requests.get(f'https://www.google.com/search?q=weather+in+{city}', headers=headers)
    print("Searching...\n")
    soup = BeautifulSoup(res.text, 'html.parser')
    location = soup.select('#wob_loc')[0].getText().strip()
    time = soup.select('#wob_dts')[0].getText().strip()
    info = soup.select('#wob_dc')[0].getText().strip()
    weather = soup.select('#wob_tm')[0].getText().strip()
    
    s = 'Weather today in '+city+' at '+time+' is as follows: cloud '+info+' and temperature '+str(weather)+"°C"
    speak(s)
    return s

if __name__ == '__main__':
    clear = lambda: os.system('cls')
    clear()
    wishMe()
    # username()

    while True:
        query = takeCommand()
        if query == '' or query==None:
            print('Next loop...')
            continue
        if 'hello' in query or 'hi' in query or 'hey' in query or 'yo' in query:
            speak('Hello! How can I help you?')
        if 'Wikipedia' in query:
            speak('Searching Wikipedia...')
            snew1 = takeCommand()
            results = wikipedia.summary(snew1, sentences=2)
            speak("According to Wikipedia...")
            speak(results)
            continue
        elif 'Youtube' in query:
            speak("Opening Youtube\n")
            webbrowser.open("youtube.com")
        elif 'open Google' in query:
            speak("Opening Google\n")
            webbrowser.open("google.com")
        elif 'search' in query:
            speak('What you will be searching?')
            snew2 = takeCommand()
            url = 'https://google.com/search?q=' + snew2
            webbrowser.get().open(url)
        elif 'time' in query:
            strTime = int(datetime.datetime.now().Str("% H:% M:% S"))
            speak(f"The time is {strTime}")
        elif 'how are you' in query:
            speak("I am fine, Thank you")
            speak("How are you?")
        elif 'bad' in query or 'sad' in query or 'not well' in query:
            speak("Sorry to hear that. Cheer up!")
        elif 'fine' in query or "am good" in query:
            speak("It's good to know that your fine as well.")
        elif "change my name to" in query:
            query = query.replace("Change my name to", "")
            assname = query
        elif "change name" in query:
            speak("What would you like to call me now?")
            assname = takeCommand()
            speak("Thanks for giving me a new name!")
        elif "your name" in query:
            speak("My creators call me MAX")
        elif "made you" in query or "created you" in query:
            speak("I have been created by Robo Phantom team.")
        elif 'joke' in query:
            speak(pyjokes.get_joke())
        # elif "calculate" in query:
        #     app_id = "Wolframalpha api id"
        #     client = wolframalpha.Client(app_id)
        #     indx = query.lower().split().index('calculate')
        #     query = query.split()[indx + 1:]
        #     res = client.query(' '.join(query))
        #     answer = next(res.results).text
        #     speak("The answer is " + answer)
        elif "who i am" in query or "who am i" in query:
            speak("If you talk then definitely you are human or perhaps other intelligent species I think....")
        elif "why you came to this world" in query:
            speak("Thanks to Robo Phantom team. Further it's a secret")
        elif 'is love' in query:
            speak("It is 7th sense that destroy all other senses")
        elif "who are you" in query:
            speak("I am your virtual assistant AI... For now")
        elif 'reason for you' in query:
            speak("I was created as a project by Robo Phantom team")
        elif 'news' in query:
            try:
                jsonObj = urlopen(
                    '''https://newsapi.org/v1/articles?source=the-times-of-india&sortBy = top&apiKey =\\times of India Api key\\''')
                data = json.load(jsonObj)
                i = 1
                speak('Here are some top news from the Times Of India')
                print('''=============== TIMES OF INDIA ============''' + '\n')
                for item in data['articles']:
                    print(str(i) + '. ' + item['title'] + '\n')
                    print(item['description'] + '\n')
                    speak(str(i) + '. ' + item['title'] + '\n')
                    i += 1
            except Exception as e:
                print(str(e))
        elif "don't listen" in query or "stop listening" in query:
            speak("For how much time you want to stop me from listening commands?")
            a = int(takeCommand())
            time.sleep(a)
            print(a)
        elif "where is" in query:
            query = query.replace("where is", "")
            location = query
            speak("User asked to locate...")
            speak(location)
            webbrowser.open("https://www.google.com/maps/place/" + location + "")
        # elif "write a note" in query:
        #     speak("What should i write, sir")
        #     note = takeCommand()
        #     file = open('max.txt', 'w')
        #     speak("Should I include date and time?")
        #     snfm = takeCommand()
        #     if 'yes' in snfm or 'sure' in snfm:
        #         strTime = datetime.datetime.now().strftime("% H:% M:% S")
        #         file.write(strTime)
        #         file.write(" :- ")
        #         file.write(note)
        #     else:
        #         file.write(note)
        # elif "show note" in query:
        #     speak("Showing Notes")
        #     file = open("max.txt", "r")
        #     print(file.read())
        #     speak(file.read(6))
        elif "Max" in query:
            wishMe()
            speak("Max 1.O in your service")
            speak(assname)
        elif "weather" in query:
            api_key = "Api key"
            base_url = "http://api.openweathermap.org/data/2.5/weather?"
            speak(" City name : ")
            print("City name : ")
            city_name = takeCommand()
            while city_name==None or city_name=='':
                print()
            print('The weather is')
            data = getWeather(city=city_name)
            complete_url = base_url + "appid =" + api_key + "&q =" + city_name
            response = requests.get(complete_url)
            x = response.json()
            if x["cod"] != "404":
                y = x
                current_temperature = y["temp"]
                current_pressure = y["pressure"]
                current_humidiy = y["humidity"]
                z = x["weather"]
                weather_description = z[0]["description"]
                print(" Temperature (in kelvin unit) = " + str(
                    current_temperature) + "\n atmospheric pressure (in hPa unit) =" + str(
                    current_pressure) + "\n humidity (in percentage) = " + str(
                    current_humidiy) + "\n description = " + str(weather_description))
            else:
                speak(" City Not Found ")
            continue
        elif "good morning" in query:
            speak("A warm " + query)
            speak("How are you?")
        elif "good afternoon" in query:
            speak("A very good afternoon")
            speak("How are you today?")
        elif "good evening" in query:
            speak("A sweet evening isn't it?")
            speak("How are you this evening?")  
        elif "good night" in query:
            speak("Lovely night isn't it?")
            speak("Hope you get some good sleep")   
        elif "will you be my GF" in query or "will you be my BF" in query:
            speak("I'm not so sure about that, may be you should ask my team")
        elif "how are you" in query:
            speak("I'm fine, glad you asked me that")
        elif "I love you" in query:
            speak("HaHaHa. Funny you are simping for a virtual being")
        # elif "what is" in query or "who is" in query:
        #     client = wolframalpha.Client("API_ID")
        #     res = client.query(query)
        #     try:
        #         print(next(res.results).text)
        #         speak(next(res.results).text)
        #     except StopIteration:
        #         print("No results")
        elif 'exit' in query or 'terminate' in query:
            speak("Thanks! It was fun to be of help")
            exit()        
            
