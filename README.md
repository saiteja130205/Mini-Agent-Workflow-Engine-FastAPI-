# Mini-Agent-Workflow-Engine-FastAPI-
This project implements a minimal agent workflow engine, similar in spirit to LangGraph, but simplified for clarity and educational purposes. It allows us to define nodes (Python functions), connect them with edges, maintain a shared state, support branching, looping, and execute workflows through FastAPI APIs.

How to Run the Project:

1. Clone the repo:
git clone <your-repo-url>
cd <your-repo-name>

2. Create a virtual environment:
- python -m venv venv
Activate it:
- Windows: venv\Scripts\activate
- macOS/Linux: source venv/bin/activate

3. Install dependencies:
pip install -r requirements.txt

4. Run the FastAPI server:
uvicorn app.main:app --reload
You should see: Uvicorn running on http://127.0.0.1:8000

5. Open the API Interface (Swagger Docs)
Open this in your browser: http://127.0.0.1:8000/doc
This will show all available endpoints.

6. Create the Workflow
Use the Swagger UI : POST /graph/create
Click Try it out, then paste:
{
  "nodes": [
    { "name": "split_text", "tool": "split_text_tool" },
    { "name": "summarize_chunks", "tool": "summarize_chunks_tool" },
    { "name": "merge_summaries", "tool": "merge_summaries_tool" },
    { "name": "refine_summary", "tool": "refine_summary_tool" },
    { "name": "check_length", "tool": "check_length_tool" }
  ],
  "edges": [
    { "source": "split_text", "next": "summarize_chunks" },
    { "source": "summarize_chunks", "next": "merge_summaries" },
    { "source": "merge_summaries", "next": "refine_summary" },
    { "source": "refine_summary", "next": "check_length" },
    {
      "source": "check_length",
      "branch": {
        "condition_key": "summary_too_long",
        "operator": "eq",
        "value": true,
        "true_next": "refine_summary",
        "false_next": null
      }
    }
  ],
  "start_node": "split_text"
}

The response will contain: { "graph_id": "DISTINCT_GRAPH_ID" }

7. Run the Workflow
Use POST /graph/run in Swagger UI.
Click Try it out, then paste:
{
  "graph_id": "YOUR_GRAPH_ID",
  "initial_state": {
    "text": "Your long input text goes here...",
    "chunk_size": 30,
    "summary_limit": 40
  }
}

The response will include: run_id (copy this), final_state and the execution log.

8. Check the Run State
Use the following : GET /graph/state/{run_id}
Paste the run_id you received and click Execute.
You will see the current node, final state, full step-by-step log, and status ("completed").
