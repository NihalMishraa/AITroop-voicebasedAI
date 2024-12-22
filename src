import sqlite3
import requests
import pyttsx3
import threading
import speech_recognition as sr
import tkinter as tk
import webbrowser
import subprocess
import face_recognition
import cv2
import pickle
import datetime
import random
import vlc
import pywhatkit as kit
from pyowm import OWM
import webbrowser
import os
import pywhatkit as kit
import random
import time
import pyautogui
from bs4 import BeautifulSoup
# Initialize pyttsx3 engine for TTS
engine = pyttsx3.init()
engine.setProperty('rate', 150)  # Set speech rate (default: 200)
engine.setProperty('volume', 1)  # Set volume (range: 0.0 to 1.0)


engine = pyttsx3.init()

# Set the rate (speed of speech)
engine.setProperty('rate', 150)  # You can change this value to speed up/slow down the speech

# Set the volume (0.0 to 1.0)
engine.setProperty('volume', 1)  
recognizer = sr.Recognizer() 
hotword = "sahayak"  # The hotword that will trigger the assistant

video_path = "C:\\Users\\amank\\Downloads\\bg.mp4"

# Initialize VLC media player
player = vlc.MediaPlayer(video_path)

# Create the Tkinter window
window = tk.Tk()
window.title("AI Assistant with Gemini Integration")
window.geometry("800x600")  # Set the window size

# Create a canvas widget for the video
canvas = tk.Canvas(window, width=800, height=600)
canvas.pack(fill="both", expand=True)

# Function to handle video restart
def video_ended(event):
    print("Video ended, restarting...")
    player.stop()
    player.play()

# Function to start the video and set up the loop
def start_video():
    # Set fullscreen and start the video
    player.set_fullscreen(True)
    media = vlc.Media(video_path)
    player.set_media(media)
    player.play()



# Farewell phrases to detect when the user says goodbye
farewell_phrases = ["bye", "goodbye", "see you", "good night", "take care", "farewell", "exit"]
def execute_system_command(command):
    try:
        if command == "shutdown":
            speak_response_local("Shutting down the system. Goodbye!")
            os.system("shutdown /s /t 1")  # Windows shutdown command
        elif command == "restart":
            speak_response_local("Restarting the system. Please wait...")
            os.system("shutdown /r /t 1")  # Windows restart command
        elif command == "sleep":
            speak_response_local("Putting the system to sleep...")
            subprocess.call("rundll32.exe powrprof.dll,SetSuspendState 0,1,0")  # Windows sleep command
        else:
            speak_response_local("Invalid command, nothing will happen.")
    except Exception as e:
        print(f"Error executing system command: {e}")
        # Function to handle farewell and shutdown/restart/sleep prompt
def handle_farewell_and_shutdown(user_query):
    if any(farewell in user_query for farewell in farewell_phrases):
        speak_response_local("Goodbye! Do you want to shut down, restart, or sleep your PC? Please say one of these options.")
        user_response = recognize_speech()

        if user_response:
            if "shutdown" in user_response:
                execute_system_command("shutdown")
            elif "restart" in user_response:
                execute_system_command("restart")
            elif "sleep" in user_response:
                execute_system_command("sleep")
            else:
                speak_response_local("No action will be taken. Goodbye!")
        else:
            speak_response_local("I didn't catch that. Goodbye!")

# Function to set the language for Text-to-Speech
def set_language(language_code):
    voices = engine.getProperty('voices')
    for voice in voices:
        if language_code in voice.languages:
            engine.setProperty('voice', voice.id)
            break

def put_system_to_sleep():
    speak_response_local("Putting the system to sleep.")
    subprocess.run("rundll32.exe powrprof.dll,SetSuspendState 0,1,0", shell=True)

# Function to shut down the system
def shut_down_system():
    speak_response_local("Shutting down the system.")
    os.system("shutdown /s /f /t 0")  # Force shutdown immediately

# Function to restart the system
def restart_system():
    speak_response_local("Restarting the system.")
    os.system("shutdown /r /f /t 0")
# Function to display the name on the GUI
def display_name_on_gui(name):
    # Assuming you have a Text widget response_text to display the name
    response_text.insert(tk.END, f"Hello, {name}!\n")

# Initialize the SQLite Database to store user info
conn = sqlite3.connect('user_data.db')  # You can change the database file name if desired
c = conn.cursor()

# Create a table for storing faces and names if it doesn't already exist
c.execute('''CREATE TABLE IF NOT EXISTS users
             (name TEXT, face_encoding BLOB, voice_encoding BLOB)''')

# Commit the changes
conn.commit()

# Initialize Text-to-Speech engine
engine = pyttsx3.init()
def clear_database():
    c.execute("DELETE FROM users")  # Delete all records from the 'users' table
    conn.commit()  # Commit changes to the database
    print("Database cleared.")
    speak_response_local("The database has been cleared and reset.", language_code="en")

# Global variables
assistant_running = False
user_name = None
conversation_history = []  # To maintain context of conversations

# Language Choices
language_choices = ['en', 'es', 'fr', 'de', 'it', 'ja']  # English, Spanish, French, German, Italian, Japanese

# Initialize the GUI window
window = tk.Tk()
window.title("AI Assistant with Gemini Integration")
window.geometry("800x600")  # Set the window size

# Create a canvas widget to draw the animated background
canvas = tk.Canvas(window, width=800, height=600)
canvas.pack(fill="both", expand=True)

# Create a Text widget to display responses
response_text = tk.Text(window, height=10, width=80)
response_text.pack(pady=20)

# Function to set the language for Text-to-Speech
def set_language(language_code):
    voices = engine.getProperty('voices')
    for voice in voices:
        if language_code in voice.languages:
            engine.setProperty('voice', voice.id)
            break

# Function to speak a response using Text-to-Speech
def speak_response_local(response, language_code='en'):
    set_language(language_code)  # Set the language for TTS
    print(f"Speaking: {response}")  # Debugging: Print what the assistant is saying
    engine.say(response)
    engine.runAndWait()

# Function to recognize speech
def recognize_speech(language='en-US'):
    recognizer = sr.Recognizer()
    recognizer.energy_threshold = 4000
    recognizer.dynamic_energy_threshold = True

    with sr.Microphone() as source:
        print("Listening for command...")
        recognizer.adjust_for_ambient_noise(source, duration=1)
        try:
            audio = recognizer.listen(source, timeout=10)  # Increase timeout for better detection
            print("Processing the audio...")

            # Use WAV format for recognition
            query = recognizer.recognize_google(audio, language=language)  # Use the passed language code
            print(f"You said: {query}")
            return query.lower()  # Return the user's command as lowercase

        except sr.UnknownValueError:
            print("Sorry, I didn't understand that.")
            return None
        except sr.RequestError:
            print("Could not request results; check your internet connection.")
            return None
        except sr.WaitTimeoutError:
            print("Listening timed out, please try again.")
            return None
def open_gmail():
    gmail_url = "https://mail.google.com"
    webbrowser.open(gmail_url)
# Function to open applications
def open_application(app_name):
    if app_name == "chrome":
        # Try opening Chrome using default browser path
        webbrowser.open("http://www.google.com")
    elif app_name == "whatsapp":
        webbrowser.open("https://web.whatsapp.com")  # Open WhatsApp Web in browser
    elif app_name == "gmail":
        webbrowser.open("https://mail.google.com")
    elif app_name == "camera":
        open_camera_and_take_picture()
    elif app_name == "youtube":
        webbrowser.open("https://www.youtube.com")
  
    else:
        speak_response_local(f"Sorry, I can't open {app_name} right now.", language_code="en")

# Function to open the camera and take a picture
def open_camera_and_take_picture():
    cap = cv2.VideoCapture(0)

    if not cap.isOpened():
        print("Error: Could not access the camera.")
        return

    print("Taking a picture...")
    ret, frame = cap.read()
    if ret:
        # Save the image to the user's Pictures directory
        save_path = os.path.expanduser('~\\Pictures\\Saved Pictures')  # Use the user's Pictures folder
        if not os.path.exists(save_path):
            os.makedirs(save_path)

        filename = f"{save_path}\\picture_{datetime.datetime.now().strftime('%Y-%m-%d_%H-%M-%S')}.jpg"
        cv2.imwrite(filename, frame)
        speak_response_local(f"Picture taken and saved as {filename}", language_code="en")
        print(f"Picture saved as {filename}")

        # Open the image using the default image viewer
        webbrowser.open(f"file:///{filename}")

    cap.release()


# Function for face recognition


# Function to compare the captured face with the database
def compare_faces_in_db(face_encoding):
    c.execute("SELECT name, face_encoding FROM users")
    rows = c.fetchall()

    for row in rows:
        stored_name = row[0]
        stored_face_encoding = pickle.loads(row[1])

        # Compare the stored face encoding with the captured face encoding
        results = face_recognition.compare_faces([stored_face_encoding], face_encoding)
        if True in results:
            return stored_name  # Return the name if a match is found

    return None  # Return None if no match is found
  # Return None if no match is found


# Function to capture user face and check if it's an old user
def capture_user_face_and_name():
    global user_name
    global assistant_running

    # Start capturing face
    print("Please look at the camera...")
    cap = cv2.VideoCapture(0)

    if not cap.isOpened():
        print("Error: Could not access the camera.")
        return

    print("Scanning face...")
    while True:
        ret, frame = cap.read()
        if not ret:
            print("Error: Failed to capture image.")
            break

        rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

        # Detect faces in the image
        face_locations = face_recognition.face_locations(rgb_frame)
        face_encodings = face_recognition.face_encodings(rgb_frame, face_locations)

        # If a face is detected, process it
        if face_locations:
            for face_encoding in face_encodings:
                # Check if this face matches any stored face
                matches = compare_faces_in_db(face_encoding)
                
                if matches:
                    # If a match is found, retrieve the name from the database
                    name = matches
                    print(f"Welcome back, {name}!")
                    speak_response_local(f"Welcome back, {name}!", language_code="en")
                    
                    # Proceed to how can I help you?
                    speak_response_local("How can I help you?", language_code="en")
                    cap.release()  # Stop the camera after recognition
                    cv2.destroyAllWindows()  # Close the window
                    assistant_running = True  # Allow command processing
                    return  # Exit the function and stop further recognition

                else:
                    # If no match, ask for name and save the face
                    print("This is a new user. What's your name?")
                    speak_response_local("This is a new user. What's your name?", language_code="en")
                    user_name = recognize_speech()

                    if user_name:
                        # Store face and name in the database
                        store_user_face_and_name(face_encoding, user_name)
                        print(f"Face captured and saved for {user_name}")
                        speak_response_local(f"Face captured and saved for {user_name}", language_code="en")
                        
                        # Proceed to greeting message
                        speak_response_local(f"Hi, {user_name}. How can I help you?", language_code="en")
                    cap.release()  # Stop the camera after recognition
                    cv2.destroyAllWindows()  # Close the window
                    assistant_running = True  # Allow command processing
                    return  # Exit the function and stop further recognition

        # Show the webcam feed
        cv2.imshow("Capturing Face", frame)

        if cv2.waitKey(1) & 0xFF == ord('q'):  # Press 'q' to exit
            break

    cap.release()
    cv2.destroyAllWindows()



   
# Function to store the user's face and name in the database
# Function to store the user's face and name in the database
def store_user_face_and_name(face_encoding, user_name):
    # Convert face encoding to a binary format (blob)
    encoded_face = pickle.dumps(face_encoding)
    
    # Insert face and name into the database
    c.execute("INSERT INTO users (name, face_encoding) VALUES (?, ?)", (user_name, encoded_face))
    conn.commit()
    print(f"User {user_name} with face encoding saved to database.")


# Function to send a WhatsApp message
def send_whatsapp_message(contact, message):
    # Use pywhatkit to open WhatsApp web and send a message
    kit.sendwhatmsg(contact, message, datetime.datetime.now().hour, datetime.datetime.now().minute + 2)  # Schedule message

    # Wait for the message input box to be ready
    time.sleep(10)  # Adjust this sleep time as necessary for WhatsApp to load fully
    
    # Use pyautogui to type the message if needed
    pyautogui.write(message)  # Type the message in the message input box
    pyautogui.press('enter')  # Press Enter to send the message

    print(f"Message sent to {contact}: {message}")
# Function to get weather information
def get_weather_info(city):
    owm = OWM('AIzaSyAu_reoQxo9TH71mFzp3yilEX5YqLkn3b0"')  # Replace with your OWM API key
    mgr = owm.weather_manager()
    observation = mgr.weather_at_place(city)
    weather = observation.weather
    temperature = weather.temperature('celsius')['temp']
    status = weather.detailed_status
    return f"The current temperature in {city} is {temperature}°C with {status}."

# Function to play music
def play_music():
    player = vlc.MediaPlayer("path_to_music.mp3")  # Replace with actual music file path
    player.play()
    speak_response_local("Playing music now.", language_code="en")

# Function to query Gemini API for a response
# Function to query Gemini API for a response
def get_gemini_response(user_query):
    api_url = "https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=AIzaSyAu_reoQxo9TH71mFzp3yilEX5YqLkn3b0"
    headers = {
        'Content-Type': 'application/json'
    }
    payload = {
        "contents": [
            {
                "parts": [{"text": user_query}]
            }
        ]
    }

    try:
        response = requests.post(api_url, json=payload, headers=headers)
        response.raise_for_status()  # Raise an exception for HTTP errors (4xx or 5xx)
        
        response_data = response.json()  # Parse the JSON response
        print("API Response:", response_data)  # Print the response to debug

        # Ensure the response contains the expected keys
        if 'content' in response_data and len(response_data['content']) > 0:
            if 'parts' in response_data['content'][0] and len(response_data['content'][0]['parts']) > 0:
                return response_data['content'][0]['parts'][0]['text']
            else:
                return "Sorry, I couldn't find any parts in the response."
        else:
            return "Sorry, I encountered an issue with the response format."
    
    except requests.exceptions.RequestException as e:
        print(f"Error while querying Gemini API: {e}")
        return "Sorry, I encountered an error while fetching the response."

    except ValueError as e:
        print(f"Error while parsing the response: {e}")
        return "Sorry, I encountered an error while processing the response."


# Function to process user commands


# Function to process user commands
def search_and_play_video(query):
    # Step 1: Use pywhatkit to search for the query on YouTube
    speak_response_local(f"Searching for {query} on YouTube, let me find something for you!", language_code="en")
    kit.search(query)  # Perform the search on YouTube
    
    # Step 2: Generate the YouTube search URL
    search_url = f"https://www.youtube.com/results?search_query={query.replace(' ', '+')}"
    
    # Step 3: Open the search URL in the browser
    speak_response_local(f"Opening YouTube results for {query}.", language_code="en")
    webbrowser.open(search_url)
    
    # Step 4: Scrape the search results page to extract video URLs
    time.sleep(2)  # Give time for the search results to load
    
    response = requests.get(search_url)
    soup = BeautifulSoup(response.text, 'html.parser')

    # Find all video links (the links to video pages are typically in <a> tags with a specific class)
    video_links = []
    
    # Loop through all anchor tags that contain a video link
    for a_tag in soup.find_all('a', href=True):
        if '/watch?' in a_tag['href']:  # Only links to YouTube video pages
            video_links.append(f"https://www.youtube.com{a_tag['href']}")

    # Step 5: If we find any video links, select one and play it
    if video_links:
        # Select a random video from the related search results (you can modify this logic if needed)
        selected_video_url = random.choice(video_links)  # Choose a random video from the list
        speak_response_local(f"Now playing a video related to {query} on YouTube.", language_code="en")
        webbrowser.open(selected_video_url)  # Open the selected video URL
    else:
        speak_response_local("Sorry, I couldn't find any videos related to that query.", language_code="en")

def listen_for_hotword():
    global is_listening
    with sr.Microphone() as source:
        recognizer.adjust_for_ambient_noise(source)  # Adjust for ambient noise
        print("Listening for hotword...")
        while True:
            try:
                audio = recognizer.listen(source, timeout=5)  # Timeout to prevent blocking
                user_input = recognizer.recognize_google(audio).lower()  # Recognize the speech

                if hotword.lower() in user_input:  # If the hotword is detected
                    if not is_listening:  # If already listening, don't trigger again
                        is_listening = True
                        print(f"Hotword detected: {hotword}!")
                        speak_response("I'm listening to your command.")
                        process_user_commands()  # Process the user's command after detecting hotword
                        is_listening = False  # Reset listening status after processing
                time.sleep(1)
            except sr.UnknownValueError:
                continue  # Ignore unknown values
            except sr.RequestError as e:
                print(f"Error with speech recognition service: {e}")
                break

def open_whatsapp():
    whatsapp_desktop_path = "C:/Users/YourUsername/AppData/Local/WhatsApp/WhatsApp.exe"  # Update path based on system

    if os.path.exists(whatsapp_desktop_path):
        print("Opening WhatsApp Desktop...")
        subprocess.Popen([whatsapp_desktop_path])  # Open WhatsApp Desktop
    else:
        print("WhatsApp Desktop not found. Opening WhatsApp Web...")
        webbrowser.open("https://web.whatsapp.com")  # Open WhatsApp Web

    # Wait for WhatsApp Web/Desktop to load (adjust time as needed)
    time.sleep(5)
def search_and_read_google_results(query):
    print(f"Searching Google for: {query}")
    search_results = list(search(query, num_results=5))  # Adjust the number of results you want

    if search_results:
        for i, url in enumerate(search_results):
            try:
                # Request the webpage and parse it using BeautifulSoup
                response = requests.get(url)
                soup = BeautifulSoup(response.text, 'html.parser')

                # Extract title and description from the page
                title = soup.title.string if soup.title else 'No title found'
                description = soup.get_text()

                # Read the title aloud first
                speak_response_local(f"Result {i + 1}: {title}")

                # Read a snippet from the description (limit to first 300 characters)
                snippet = description[:300]  # Adjust snippet length as necessary
                speak_response_local(f"Here is a snippet: {snippet}")

            except Exception as e:
                print(f"Error fetching URL {url}: {e}")
                speak_response_local(f"Sorry, I could not retrieve results from {url}.")
    else:
        speak_response_local("Sorry, I couldn't find any results.")



# Function to process user commands
def process_user_commands():
    global assistant_running
    with sr.Microphone() as source:
        recognizer.adjust_for_ambient_noise(source)
        print("Listening for hotword...")
    while assistant_running:
        user_query = recognize_speech()

        if user_query:
            handle_farewell_and_shutdown(user_query)
            if "clear the database" in user_query:
                clear_database()
            # Check for Farewell Commands
            if any(farewell in user_query for farewell in farewell_phrases):
                farewell_response = "Goodbye! Have a great day ahead!"  # Farewell message
                speak_response_local(farewell_response)
                print(farewell_response)
                assistant_running = False  # Stop the assistant

                # Close the GUI window
                window.quit()
                window.destroy()  # Close the Tkinter window
                
                # Exit the loop and end the assistant
                continue
            
            # Check if the query matches predefined commands (open applications)
            elif "open" in user_query:
                if "chrome" in user_query:
                    open_application("chrome")
                elif "whatsapp" in user_query:
                    open_application("whatsapp")
                elif "gmail" in user_query:                
                    open_application("gmail")
                elif "camera" in user_query:
                    open_application("camera")
                elif "youtube" in user_query:
                    speak_response_local("What do you want to search? Let me know and I will search it for you on YouTube.", language_code="en")
                    search_query = recognize_speech()  # Ask the user what to search
                    if search_query:
                        search_and_play_video(search_query)  # Search and play a video
                    else:
                        speak_response_local("Sorry, I didn't hear what you want to search for. Please try again.", language_code="en")
                else:
                    speak_response_local("I am unable to open that application.")

            # Other predefined functionalities
            elif "weather" in user_query:
                response = get_weather_info('New York')  # Example city
                response_text.insert(tk.END, response + '\n')
                speak_response_local(response)
            elif "music" in user_query:
                play_music()
            elif "message" in user_query:
                contact = "John"  # Example contact name
                message = "Hello from Gemini assistant!"  # Example message
                send_whatsapp_message(contact, message)
            elif "stop" in user_query:
                assistant_running = False
                speak_response_local("Assistant has stopped.", language_code="en")
                break
            else:
                # If the command is not recognized, query Gemini API
                gemini_response = get_gemini_response(user_query)
                response_text.insert(tk.END, gemini_response + '\n')  # Display the Gemini response
                speak_response_local(gemini_response)  # Read aloud the response

        time.sleep(1)  # Small delay to prevent high CPU usage in the loop


# Function to start assistant automatically
def start_assistant():
    print("Assistant started. Listening for the hotword 'Sahayak'.")
    threading.Thread(target=listen_for_hotword, daemon=True).start() 
    global assistant_running
    assistant_running = True

    # Greet and ask for name if it's the first time
    speak_response_local("Hi, I'm your Sahayak, how can I help you?", language_code="en")
    
    # Capture face and name
    capture_user_face_and_name()
    
    # Process user commands
    threading.Thread(target=process_user_commands, daemon=True).start()

# Run the assistant as soon as the program starts
start_assistant()
player.event_manager().event_attach(vlc.EventType.MediaPlayerEndReached, video_ended)

# Start video in the background
start_video()

window.mainloop()
