
## A minimal UI
This project includes a simple **React** front end that sends the user’s question to a FastAPI back end and streams the agent’s response in real time. To run the UI:

1- Open a terminal and start the Ollama server: `ollama serve`.

2- In a second terminal, navigate to the frontend folder and install dependencies:`npm install`.

3- In the same terminal, navigate to the backend folder and start the FastAPI back‑end: `uvicorn app:app --reload --port 8000`

4- Open a third terminal, navigate to the frontend folder, and start the React dev server: `npm run dev`

5- Visit `http://localhost:5173/` in your browser.



## 🎉 Congratulations!

* You have built a **web‑enabled agent**: tool calling → JSON schema → LangChain ReAct → web search → simple UI.
* Try adding more tools, such as news or finance APIs.
* Experiment with multiple tools, different models, and measure accuracy vs. hallucination.


👏 **Great job!** Take a moment to celebrate. The techniques you implemented here power many production agents and chatbots.