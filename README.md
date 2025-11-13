# Building Ai course project
<!-- This is the markdown template for the final project of the Building AI course, 
created by Reaktor Innovations and University of Helsinki. 
Copy the template, paste it to your GitHub README and edit! -->

# Project Title

Final project for the Building AI course

## from flask import Flask, request, render_template_string, jsonify
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import argparse

# Simple in-memory ideas loader
def load_ideas(path="ideas.txt"):
    with open(path, "r", encoding="utf-8") as f:
        ideas = [line.strip() for line in f if line.strip()]
    return ideas

# Suggest function
def suggest(input_text, ideas, topk=5):
    corpus = ideas + [input_text]
    vect = TfidfVectorizer().fit_transform(corpus)
    sims = cosine_similarity(vect[-1], vect[:-1])[0]
    ranked_idx = sims.argsort()[::-1][:topk]
    return [(ideas[i], float(sims[i])) for i in ranked_idx]

# Web app
app = Flask(__name__)
HTML = '''
<!doctype html>
<title>AI Idea Generator</title>
<h1>AI Idea Generator</h1>
<form method="post">
  <label>اكتب وصفًا أو كلمات مفتاحية:</label><br>
  <input type="text" name="text" style="width:60%" required>
  <input type="submit" value="Suggest">
</form>
{% if suggestions %}
  <h2>اقتراحات:</h2>
  <ol>
  {% for s,score in suggestions %}
    <li>{{s}} <small>({{ "%.2f"|format(score) }})</small></li>
  {% endfor %}
  </ol>
{% endif %}
<p>يمكنك أيضًا استخدام API عبر POST إلى /api/suggest مع JSON {"text":"..."}.</p>
'''

@app.route("/", methods=["GET","POST"])
def index():
    ideas = load_ideas()
    suggestions = None
    if request.method == "POST":
        text = request.form.get("text","")
        suggestions = suggest(text, ideas, topk=5)
    return render_template_string(HTML, suggestions=suggestions)

@app.route("/api/suggest", methods=["POST"])
def api_suggest():
    data = request.get_json() or {}
    text = data.get("text","")
    ideas = load_ideas()
    suggestions = suggest(text, ideas, topk=5)
    return jsonify([{"idea":s,"score":sc} for s,sc in suggestions])

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--text", type=str, help="Run suggest on a given text and print results")
    args = parser.parse_args()
    if args.text:
        ideas = load_ideas()
        print(suggest(args.text, ideas, topk=5))
    else:
        app.run(debug=True)