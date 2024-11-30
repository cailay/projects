#run a python code on linux server on aws ec2 instance
#cat notify.py
import os
import requests
from bs4 import BeautifulSoup
from twilio.rest import Client
import time

# Configuration
url = "https://www."
check_interval = 3600  # Check every hour

# Environment variables for sensitive information
twilio_sid = os.getenv("TWILIO_SID", "")
twilio_auth_token = os.getenv("TWILIO_AUTH_TOKEN", "")
twilio_phone_number = os.getenv("TWILIO_PHONE_NUMBER", "")
recipient_phone_number = os.getenv("RECIPIENT_PHONE_NUMBER", "")

def check_registration_status():
    try:
        print("Fetching the registration page...")
        response = requests.get(url)
        response.raise_for_status()  # Raise an error for bad status codes
        soup = BeautifulSoup(response.content, 'html.parser')
        registration_text = soup.get_text()
        print("Fetched and parsed the registration page successfully.")

        # Adjust this condition based on the specific text change when registrations open
        if "Aug. 19, 2024" not in registration_text:
            print("Registrations are open.")
            return True
        else:
            print("Registrations are not open yet.")
            return False
    except requests.RequestException as e:
        print(f"An error occurred while fetching the page: {e}")
        return False

def send_sms_notification(message_body):
    try:
        client = Client(twilio_sid, twilio_auth_token)
        message = client.messages.create(
            body=message_body,
            from_=twilio_phone_number,
            to=recipient_phone_number
        )
        print(f"Notification sent successfully. Message SID: {message.sid}")
    except Exception as e:
        print(f"An error occurred while sending the SMS: {e}")

if __name__ == "__main__":
    while True:
        if check_registration_status():
            send_sms_notification("Registrations are now open!")
            break  # Exit the loop after sending the notification
        else:
            send_sms_notification("Registrations are not open yet. Checking again in an hour.")
        time.sleep(check_interval)
