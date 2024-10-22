import subprocess
import wolframalpha
import tkinter as tk
from tkinter import simpledialog
import json
import random
import operator
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
import winshell
import pyjokes
import feedparser
import smtplib
import ctypes
import time
import requests
import shutil
from twilio.rest import Client
from clint.textui import progress
from ecapture import ecapture as ec
from bs4 import BeautifulSoup
import win32com.client as wincl
from urllib.request import urlopen


def display_message(text):
    # Display message in chat window
    chat_window.config(state=tk.NORMAL)  # Enable text box to modify
    chat_window.insert(tk.END, text + "\n")  # Insert message
    chat_window.config(state=tk.DISABLED)  # Disable text box to prevent editing
    chat_window.yview(tk.END)  # Scroll to the end


def takeCommand():
    user_input = user_entry.get()
    user_entry.delete(0, tk.END)
    display_message("You: " + user_input)
    return user_input.lower()


def wishMe():
    display_message("Bot: Hello! What's your name?")


def sendEmail(to, content):
    # Update email credentials here
    server = smtplib.SMTP('smtp.gmail.com', 587)
    server.ehlo()
    server.starttls()
    server.login('your email id', 'your email password')
    server.sendmail('your email id', to, content)
    server.close()


def processQuery(query):
    global user_name

    if user_name is None:
        # If user_name is not set, set it here and personalize further interactions
        user_name = query.capitalize()
        display_message(f"Bot: Nice to meet you, {user_name}! How can I assist you today?")
        return

    if 'wikipedia' in query:
        display_message(f'Bot: Searching Wikipedia for you, {user_name}...')
        query = query.replace("wikipedia", "")
        results = wikipedia.summary(query, sentences=3)
        display_message("Bot: According to Wikipedia")
        display_message("Bot: " + results)

    elif 'open youtube' in query:
        display_message(f"Bot: Opening YouTube for you, {user_name}")
        webbrowser.open("youtube.com")
    

    elif 'open google' in query:
        display_message(f"Bot: Opening Google for you, {user_name}")
        webbrowser.open("google.com")

    elif 'play music' in query:
        display_message(f"Bot: Playing music for you, {user_name}")
        music_dir = "C:\\Users\\GAURAV\\Music"
        songs = os.listdir(music_dir)
        os.startfile(os.path.join(music_dir, songs[0]))

    elif 'the time' in query:
        strTime = datetime.datetime.now().strftime("%H:%M:%S")
        display_message(f"Bot: {user_name}, the time is {strTime}")

    elif 'send a mail' in query:
        try:
            display_message(f"Bot: What should I say in the email, {user_name}?")
            content = takeCommand()
            to = simpledialog.askstring("Input", "Receiver email: ")
            sendEmail(to, content)
            display_message(f"Bot: Email has been sent, {user_name}!")
        except Exception as e:
            display_message(f"Bot: Sorry {user_name}, I am not able to send this email.")

    elif 'how are you' in query:
        display_message(f"Bot: I am fine, thank you {user_name}.")
        display_message(f"Bot: How are you, {user_name}?")

    elif 'calculate' in query:
        app_id = "Wolframalpha API key"
        client = wolframalpha.Client(app_id)
        indx = query.lower().split().index('calculate')
        query = query.split()[indx + 1:]
        res = client.query(' '.join(query))
        answer = next(res.results).text
        display_message(f"Bot: The answer is {answer}, {user_name}")

    elif 'joke' in query:
        display_message("Bot: " + pyjokes.get_joke())
    
    elif "day" in query:
        current_date = datetime.datetime.now()
        day_of_week = current_date.strftime("%A")
        display_message(f"Bot: Today is {day_of_week}.")
    
    elif "hi" or "hello" or "hey" or "namaste":
        display_message(f"Bot: Namaste {user_name}. How can I help you today?")

    elif 'news' in query:
        try:
            jsonObj = urlopen('''https://newsapi.org/v1/articles?source=the-times-of-india&sortBy=top&apiKey=YOUR_NEWS_API_KEY''')
            data = json.load(jsonObj)
            i = 1
            display_message(f'Bot: Here are some top news from the Times of India, {user_name}')
            for item in data['articles']:
                display_message(f"Bot: {i}. {item['title']}")
                i += 1
        except Exception as e:
            display_message(f"Bot: {str(e)}")

    elif "exit" in query:
        display_message(f"Bot: Thanks for using the chatbot, {user_name}!")
        root.quit()

    else:
        display_message(f"Bot: I'm sorry, I don't understand that command, {user_name}.")


def on_enter_pressed(event):
    user_input = takeCommand()  # Get user input from entry
    processQuery(user_input)  # Process the input


# Global variable for storing user's name
user_name = None

# GUI setup
root = tk.Tk()
root.title("Chatbot")

chat_window = tk.Text(root, bd=1, bg="light yellow", width=100, height=25, font=("Arial", 12))
chat_window.pack(padx=10, pady=10)
chat_window.config(state=tk.DISABLED)  # Initially disable the text box

user_entry = tk.Entry(root, bd=1, bg="white", width=29, font=("Arial", 12))
user_entry.pack(pady=10)
user_entry.bind("<Return>", on_enter_pressed)

wishMe()

root.mainloop()
