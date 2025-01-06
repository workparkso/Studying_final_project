찾아보니 RAG와 OPENAPI를 연동하는 방법을 찾게 되었다.
이는 VS CODE와 Python를 이용해서 나타낼 수 있다.
이를 참고해서 코드를 생각해보면, 보다 최종 프로젝트에 도움이 될 지도 모른다는 생각에 공부를 하게 되었다.

-------------


Here's a more organized version of your guide to building the app with PostgreSQL, Docker, OpenAI, and RAG. I've restructured it with clean formatting and added sections for clarity.

---

## 1. Set Up Docker Environment

### Docker Compose Configuration

Create a `docker-compose.yml` file to set up the PostgreSQL service.

```yaml
version: '3.8'

services:
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: chatbot_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Run the following command to start the container:

```bash
docker-compose up -d
```

---

## 2. Set Up PostgreSQL with DBeaver

- **Host:** `localhost`
- **Port:** `5432`
- **Database:** `chatbot_db`
- **User:** `user`
- **Password:** `password`

Use DBeaver to connect to your PostgreSQL database.

---

## 3. Create Database Schema

Run this SQL query to create the schema for storing conversations:

```sql
CREATE TABLE conversations (
    id SERIAL PRIMARY KEY,
    user_message TEXT NOT NULL,
    bot_response TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 4. Python App with OpenAI API & RAG

### Install Required Libraries

Install the necessary libraries using `pip`:

```bash
pip install openai psycopg2-binary sqlalchemy
```

---

## 5. Python Code for RAG and OpenAI Integration

### Structure for Integrating OpenAI API with Retrieval-Augmented Generation (RAG)

```python
import openai
import psycopg2
from sqlalchemy import create_engine

# OpenAI API key
openai.api_key = 'your-openai-api-key'

# PostgreSQL connection setup
conn = psycopg2.connect(
    dbname='chatbot_db',
    user='user',
    password='password',
    host='localhost',
    port='5432'
)

# Function to retrieve relevant context from database
def get_context_from_db(query):
    engine = create_engine('postgresql+psycopg2://user:password@localhost:5432/chatbot_db')
    with engine.connect() as connection:
        result = connection.execute(f"SELECT * FROM conversations WHERE user_message LIKE '%{query}%' ORDER BY created_at DESC LIMIT 3")
        context = ""
        for row in result:
            context += f"User: {row['user_message']}\nBot: {row['bot_response']}\n"
        return context

# Function to generate a chatbot response using OpenAI with context (RAG)
def generate_chatbot_response(user_message):
    context = get_context_from_db(user_message)
    
    prompt = f"""
    You are a helpful assistant. Based on the previous conversation, generate a relevant response.

    Previous Context: {context}
    
    User's current message: {user_message}
    
    Respond as the chatbot: 
    """

    response = openai.Completion.create(
        engine="text-davinci-003",  # Or use another model, depending on your needs
        prompt=prompt,
        max_tokens=150
    )
    return response.choices[0].text.strip()

# Function to store the conversation in the database
def store_conversation(user_message, bot_response):
    with conn.cursor() as cursor:
        cursor.execute(
            "INSERT INTO conversations (user_message, bot_response) VALUES (%s, %s)",
            (user_message, bot_response)
        )
        conn.commit()

# Main chatbot loop
if __name__ == "__main__":
    while True:
        user_message = input("You: ")
        bot_response = generate_chatbot_response(user_message)
        print("Bot:", bot_response)
        store_conversation(user_message, bot_response)
```

---

## 6. Dockerize the App (Optional)

### Dockerfile

Create a `Dockerfile` to Dockerize the application:

```dockerfile
# Use official Python image from Docker Hub
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application files
COPY . .

# Expose the port the app runs on
EXPOSE 5000

# Command to run the app
CMD ["python", "app.py"]
```

Create a `requirements.txt` file with the following contents:

```txt
openai
psycopg2-binary
sqlalchemy
```

---

## 7. Running the Application

1. Set up Docker and PostgreSQL.
2. Run the Python application using the following command:

```bash
python app.py
```

3. Start interacting with your AI-powered chatbot, which will use context from past conversations (RAG) to generate responses.

You can extend this application by adding more sophisticated database queries, error handling, and additional features like an API.

