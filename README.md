# AI सहायक (AI Assistant) - Python प्रोग्राम
# यह प्रोग्राम एक बुद्धिमान AI सहायक है, जो उपयोगकर्ता से संवाद करता है, भावनाओं का विश्लेषण करता है,
# और ज्ञान प्राप्त करने, गोपनीयता प्रबंधन और अन्य सुविधाओं का समर्थन करता है।

from transformers import pipeline
from cryptography.fernet import Fernet
import requests

# भावनात्मक विश्लेषण के लिए पाइपलाइन (Sentiment Analysis Pipeline)
# This pipeline analyzes the sentiment (emotion) of user input.
sentiment_analyzer = pipeline("sentiment-analysis")

# गोपनीयता के लिए एन्क्रिप्शन कुंजी बनाना (Generate encryption key for privacy)
key = Fernet.generate_key()
cipher_suite = Fernet(key)

# एडमिन उपयोगकर्ताओं की सूची (List of Admin Users)
admins = ["admin_username"]

# भावनाओं का विश्लेषण करने का कार्य (Function to Analyze Emotions)
def analyze_emotion(user_input):
    # उपयोगकर्ता द्वारा दिए गए इनपुट का विश्लेषण करें
    result = sentiment_analyzer(user_input)
    return result[0]['label']

# भावनाओं को संभालने का कार्य (Function to Handle Emotions)
def handle_emotion(emotion):
    # उपयोगकर्ता की भावनाओं के आधार पर प्रतिक्रिया दें
    responses = {
        "POSITIVE": "😊 आप खुश हैं, यह सुनकर मुझे बहुत अच्छा लगा! (You are happy, I am glad to hear that!)",
        "NEGATIVE": "😔 आप परेशान लग रहे हैं। मैं आपकी कैसे मदद कर सकता हूँ? (You seem upset. How can I assist you?)",
        "NEUTRAL": "😐 मैं आपकी बात समझ रहा हूँ। चलिए आगे बात करते हैं। (I understand. Let's continue talking.)"
    }
    return responses.get(emotion, "❓ मैं आपकी भावनाओं को समझने में कठिनाई महसूस कर रहा हूँ। (I am having trouble understanding your emotions.)")

# बाहरी स्रोत से ज्ञान प्राप्त करना (Fetch Knowledge from an External Source)
def fetch_knowledge(topic):
    # Replace this URL with an actual API endpoint for fetching knowledge
    url = f"https://api.example.com/learn?topic={topic}"
    try:
        response = requests.get(url)
        if response.status_code == 200:
            return response.json().get("data", "❌ कोई जानकारी नहीं मिली। (No information found.)")
        else:
            return "❌ API से डेटा प्राप्त करने में समस्या है। (There is an issue fetching data from the API.)"
    except Exception as e:
        return f"⚠️ त्रुटि: {str(e)} (Error: {str(e)})"

# संदेश एन्क्रिप्ट करने का कार्य (Function to Encrypt a Message)
def encrypt_message(message):
    return cipher_suite.encrypt(message.encode()).decode()

# एन्क्रिप्टेड संदेश को डिक्रिप्ट करने का कार्य (Function to Decrypt a Message)
def decrypt_message(encrypted_message):
    return cipher_suite.decrypt(encrypted_message.encode()).decode()

# निजी संदेश को संभालने का कार्य (Function to Handle Private Messages)
def private_message(user, message):
    # केवल एडमिन उपयोगकर्ताओं को निजी संदेश देखने की अनुमति है
    if user in admins:
        encrypted_message = encrypt_message(message)
        return f"🔒 एन्क्रिप्टेड संदेश: {encrypted_message} (Encrypted Message: {encrypted_message})"
    else:
        return "❌ आपको इस डेटा को देखने की अनुमति नहीं है। (You are not authorized to view this data.)"

# मुख्य सहायक कार्य (Main Assistant Function)
def chat_with_ai(user_input, user):
    # उपयोगकर्ता की भावनाओं का विश्लेषण करें (Analyze user emotions)
    emotion = analyze_emotion(user_input)
    response = handle_emotion(emotion)
    
    # ज्ञान प्राप्त करने के लिए इनपुट की जाँच करें (Check for knowledge fetching commands)
    if "learn" in user_input.lower():
        topic = user_input.split("learn about ")[-1]
        knowledge = fetch_knowledge(topic)
        response += f" 🧠 यहाँ कुछ जानकारी है {topic} के बारे में: {knowledge} (Here is some information about {topic}: {knowledge})"

    # निजी संदेश को संभालें (Handle private messages)
    if "private" in user_input.lower():
        private_data = "यह एक प्राइवेट संदेश है। (This is a private message.)"
        response = private_message(user, private_data)
    
    return response

# सहायक प्रारंभ करें (Run the Assistant)
if __name__ == "__main__":
    print("🤖 AI सहायक चालू हो चुका है। 'exit' टाइप करें बंद करने के लिए। (AI Assistant is now running. Type 'exit' to stop.)")
    while True:
        user_input = input("आप: ")  # (You: )
        if user_input.lower() == "exit":
            print("👋 AI सहायक बंद हो रहा है। (AI Assistant is shutting down.)")
            break
        user = "admin_username"  # Replace this with actual user management logic
        print("AI सहायक: ", chat_with_ai(user_input, user))  # (AI Assistant: )
