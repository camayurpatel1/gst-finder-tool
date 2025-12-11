import streamlit as st
import google.generativeai as genai

# --- API KEY SETUP ---
# This line grabs the key from the "Secrets" box you set up in Streamlit Cloud
try:
    API_KEY = st.secrets["GOOGLE_API_KEY"]
except FileNotFoundError:
    st.error("API Key not found. Please set GOOGLE_API_KEY in Advanced Settings > Secrets.")
    st.stop()

# Configure the AI
genai.configure(api_key=API_KEY)
model = genai.GenerativeModel('gemini-1.5-flash')

# --- THE APP DESIGN ---
st.set_page_config(page_title="GST HSN Finder", page_icon="🔍")

st.title("🔍 Smart GST HSN Code Finder")
st.markdown("Type a product description below to get the **HSN Code** and **GST Rate**.")

# Input Box
product_name = st.text_input("Enter Product Name (e.g., 'Frozen Paratha' or 'Leather Handbag')", "")

if st.button("Find HSN Code"):
    if product_name:
        with st.spinner("Searching GST Database..."):
            try:
                # The Prompt
                prompt = f"""
                You are an expert Chartered Accountant in India. 
                Identify the HSN Code and GST Rate for this product: '{product_name}'.
                
                Provide the output in this format:
                1. **HSN Code**: [Code]
                2. **GST Rate**: [Percentage]
                3. **Reasoning**: [Short explanation of why this classification applies]
                """
                
                # Get Answer
                response = model.generate_content(prompt)
                st.success("Found!")
                st.write(response.text)
                
            except Exception as e:
                st.error(f"Error: {e}")
    else:
        st.warning("Please enter a product name first.")