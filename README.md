import streamlit as st
from langchain.schema import HumanMessage, SystemMessage, AIMessage
from langchain_openai import ChatOpenAI
from dotenv import load_dotenv
import os

# Load environment variables
load_dotenv()
OPENAI_API_KEY = os.getenv('OPENAI_API_KEY')

# Initialize the OpenAI Chat model with a temperature setting
chat = ChatOpenAI(temperature=0.7)

# Streamlit configuration
st.set_page_config(page_title="Therapist Chatbot")
st.header("Hello, I am your Therapist Chatbot")

# Initialize chat history if not already present
if 'flowmessages' not in st.session_state:
    st.session_state['flowmessages'] = [
        SystemMessage(content="You are a compassionate and empathetic therapist AI assistant.")
    ]

# Streamlit UI for user interaction
input_text = st.text_input("What's on your mind?", key="input")

if st.button("Share your thoughts"):
    if input_text:
        # Add user message to the flow
        st.session_state['flowmessages'].append(HumanMessage(content=input_text))

        # Get AI response
        response = chat(st.session_state['flowmessages'])

        # Append AI response to the chat history
        st.session_state['flowmessages'].append(AIMessage(content=response.content))

        # Display chat history
        for message in st.session_state['flowmessages']:
            if isinstance(message, HumanMessage):
                st.write(f"**You:** {message.content}")
            elif isinstance(message, AIMessage):
                st.write(f"**Therapist:** {message.content}")
    else:
        st.warning("Please enter a message.")
