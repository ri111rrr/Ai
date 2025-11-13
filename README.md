# Building Ai course project
<!-- This is the markdown template for the final project of the Building AI course, 
created by Reaktor Innovations and University of Helsinki. 
Copy the template, paste it to your GitHub README and edit! -->

# Project Title

Final project for the Building AI course

HTML = '''
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Idea Generator</title>
<style>
  body { font-family: Arial, sans-serif; margin: 40px; }
  input[type=text] { width: 60%; padding: 8px; font-size: 16px; }
  input[type=submit] { padding: 8px 16px; font-size: 16px; }
  ol { margin-top: 20px; }
  li { margin-bottom: 10px; }
  small { color: #555; }
</style>
</head>
<body>
<h1>AI Idea Generator</h1>
<form method="post">
  <label>Enter a description or keywords:</label><br>
  <input type="text" name="text" required>
  <input type="submit" value="Suggest">
</form>
{% if suggestions %}
  <h2>Suggestions:</h2>
  <ol>
  {% for s, score in suggestions %}
    <li>{{ s }} <small>({{ "%.2f"|format(score) }})</small></li>
  {% endfor %}
  </ol>
{% endif %}
<p>You can also use the API via POST to /api/suggest with JSON {"text":"..."}</p>
</body>
</html>
'''