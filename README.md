import speech_recognition as sr
import pyttsx3
import webbrowser
import wikipedia
import datetime

class VirtualAssistant:
  def __init__(self):
        self.engine = pyttsx3.init()
        self.recognizer = sr.Recognizer()

  def speak(self, text):
        """Converts text to speech."""
        print(f"Assistant: {text}")
        self.engine.say(text)
        self.engine.runAndWait()

  def listen(self):
        """Listens for user input via microphone."""
        with sr.Microphone() as source:
            print("Listening...")
            self.recognizer.adjust_for_ambient_noise(source)
            try:
                audio = self.recognizer.listen(source, timeout=5)
                command = self.recognizer.recognize_google(audio).lower()
                print(f"You: {command}")
                return command
            except sr.UnknownValueError:
                self.speak("Sorry, I couldn't understand. Can you repeat?")
                return ""
            except sr.RequestError:
                self.speak("Sorry, my speech service is not working.")
                return ""

   
  def search_wikipedia(self, query):
        """Search Wikipedia for a topic."""
        try:
            self.speak(f"Searching Wikipedia for {query}")
            result = wikipedia.summary(query, sentences=2)
            self.speak(result)
        except wikipedia.exceptions.DisambiguationError as e:
            self.speak("Multiple results found. Please be more specific.")
            print(e.options)
        except wikipedia.exceptions.PageError:
            self.speak("No Wikipedia page found for this topic.")
        except Exception as e:
            self.speak(f"An error occurred: {e}")

   def open_website(self, site):
        """Open a website in a browser."""
        url = f"https://{site}"
        self.speak(f"Opening {site}")
        webbrowser.open(url)

   def tell_time(self):
        """Tells the current time."""
        now = datetime.datetime.now().strftime("%I:%M %p")
        self.speak(f"The time is {now}")

  def process_command(self, command):
        """Processes user commands."""
        if "wikipedia" in command:
            topic = command.replace("wikipedia", "").strip()
            if topic:
                self.search_wikipedia(topic)
            else:
                self.speak("Please specify a topic.")
        
  elif "open" in command:
            site = command.replace("open", "").strip()
            if site:
                self.open_website(site)
            else:
                self.speak("Please specify a website.")

  elif "time" in command:
            self.tell_time()
            
  elif "exit" in command or "stop" in command:
            self.speak("Goodbye!")
            return False  # Stops the loop

  else:
            self.speak("I don't understand that command.")
            return True  # Continue running

def main():
    assistant = VirtualAssistant()
    assistant.speak("Hello! How can I assist you?")

  running = True
    while running:
        command = assistant.listen()
        if command:
            running = assistant.process_command(command)

if __name__== "__main__":
    main()
