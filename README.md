# Medieval Text Annotator - User Manual

## Installation & Setup

### Step 1: Install Dependencies
```bash
pip install google-generativeai pandas lxml backoff
```

### Step 2: Download the Code
Save the complete implementation as `medieval_annotator.py` in your project directory.

### Step 3: Create Your Script
Create a new Python file (e.g., `my_annotation_script.py`) and import the module:

```python
# Import the module
from medieval_annotator import MedievalTextAnnotator

# Initialize with your API key
annotator = MedievalTextAnnotator(api_key="your_gemini_api_key_here")

# Your text to annotate
text = "Teresa de Cartagena escribe en el convento de San Francisco..."

# Annotate the text
result = annotator.annotate_text(text)

# Print results
print(f"Total annotations: {result.total_annotations}")
print(f"Processing time: {result.processing_time:.2f}s")
```

### Step 4: Run Your Script
```bash
python my_annotation_script.py
```

## Project Structure
```
your_project/
├── medieval_annotator.py    # The main code (copy from implementation)
├── my_script.py            # Your custom script
├── output/                 # Directory for saved files
└── requirements.txt        # Dependencies
```

## Getting Gemini API Key
1. Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Create a new API key
3. Copy the key and use it in your script

## Complete Setup Example

### requirements.txt
```
google-generativeai>=0.3.0
pandas>=1.5.0
lxml>=4.9.0
backoff>=2.2.0
```

### my_annotation_script.py
```python
#!/usr/bin/env python3
"""
Example script showing how to use the Medieval Text Annotator
"""

# Import the annotator
from medieval_annotator import MedievalTextAnnotator

def main():
    # Replace with your actual Gemini API key
    API_KEY = "your_gemini_api_key_here"
    
    # Initialize the annotator
    annotator = MedievalTextAnnotator(api_key=API_KEY)
    
    # Sample medieval Spanish text
    text = """
    En el nombre de Dios todopoderoso, yo Teresa de Cartagena, 
    monja profesa en el monasterio de San Francisco, 
    escribo esta obra para consolar a las almas devotas 
    que sufren en este valle de lágrimas.
    """
    
    # Validate the text first
    is_valid, error_msg = annotator.validate_text(text)
    if not is_valid:
        print(f"Text validation failed: {error_msg}")
        return
    
    print("Starting annotation process...")
    
    # Annotate the text
    result = annotator.annotate_text(text)
    
    # Print results
    print(f"\nAnnotation Results:")
    print(f"- Total annotations: {result.total_annotations}")
    print(f"- Processing time: {result.processing_time:.2f} seconds")
    print(f"- Chunks processed: {result.chunk_count}")
    
    # Save results to files
    if result.total_annotations > 0:
        file_paths = annotator.save_results(
            result=result,
            output_dir="./output",
            base_filename="my_annotations"
        )
        
        print(f"\nFiles saved:")
        for format_type, path in file_paths.items():
            print(f"- {format_type.upper()}: {path}")
    
    # Show statistics
    stats = annotator.get_statistics(result)
    print(f"\nAnnotation Statistics:")
    for annotation_type, count in stats.get('type_counts', {}).items():
        print(f"- {annotation_type}: {count}")

if __name__ == "__main__":
    main()
```

### Run the script
```bash
python my_annotation_script.py
```

### MedievalTextAnnotator Class

#### Constructor
```python
annotator = MedievalTextAnnotator(
    api_key="your_gemini_api_key",
    model_name="gemini-1.5-flash"  # Optional, default shown
)
```

#### Main Methods

##### Single Text Annotation
```python
result = annotator.annotate_text(
    text="Your Spanish/Catalan text here",
    max_chunk_size=1500  # Optional, default shown
)
```

##### Batch Processing
```python
texts = ["Text 1", "Text 2", "Text 3"]
results = annotator.annotate_batch(
    texts=texts,
    max_chunk_size=1500  # Optional
)
```

##### Utility Methods
```python
# Validate input text
is_valid, error_msg = annotator.validate_text(text)

# Get taxonomy structure
taxonomy = annotator.get_taxonomy()

# Extract specific outputs
xml_output = annotator.get_xml_output(result)
csv_dataframe = annotator.get_csv_output(result)
json_data = annotator.get_json_output(result)

# Save results to files
file_paths = annotator.save_results(
    result=result,
    output_dir="./output",
    base_filename="my_annotations"
)

# Get detailed statistics
stats = annotator.get_statistics(result)
```

### AnnotationResult Object

```python
# Available attributes
result.xml_output          # XML string with embedded annotations
result.csv_output          # pandas DataFrame
result.json_output         # Dictionary for ML training
result.total_annotations   # Integer count
result.processing_time     # Float seconds
result.chunk_count         # Integer
result.statistics          # Dictionary with detailed stats
result.chunks              # List of ChunkAnnotation objects
```

## Deployment Options

### Option 1: Local Desktop Script
- Save `medieval_annotator.py` in your project folder
- Create your custom script (see example above)
- Run with: `python my_script.py`

### Option 2: Jupyter Notebook
```python
# In a Jupyter cell
%pip install google-generativeai pandas lxml backoff

# Copy the medieval_annotator.py code to a cell, then:
annotator = MedievalTextAnnotator(api_key="your_key")
result = annotator.annotate_text("Your text here")
```

### Option 3: Google Colab
```python
# In Colab
!pip install google-generativeai pandas lxml backoff

# Upload medieval_annotator.py or copy the code
# Then use as normal
```

### Option 4: Web Service (Flask)
```python
from flask import Flask, request, jsonify
from medieval_annotator import MedievalTextAnnotator

app = Flask(__name__)
annotator = MedievalTextAnnotator(api_key="your_key")

@app.route('/annotate', methods=['POST'])
def annotate():
    text = request.json.get('text')
    result = annotator.annotate_text(text)
    return jsonify(result.json_output)

if __name__ == '__main__':
    app.run(debug=True)
```

## Complete API Reference

### Example 1: Basic Annotation
```python
from medieval_annotator import MedievalTextAnnotator

# Initialize
annotator = MedievalTextAnnotator(api_key="your_api_key")

# Sample medieval text
text = """
En el nombre de Dios todopoderoso, yo Teresa de Cartagena, 
monja profesa en el monasterio de San Francisco, 
escribo esta obra para consolar a las almas devotas.
"""

# Annotate
result = annotator.annotate_text(text)

# Print results
print(f"Found {result.total_annotations} annotations")
print(f"Processed in {result.processing_time:.2f} seconds")
```

### Example 2: Batch Processing
```python
# Multiple texts
texts = [
    "Texto medieval español número uno...",
    "Segon text medieval en català...",
    "Tercer texto con contenido religioso..."
]

# Process all texts
results = annotator.annotate_batch(texts)

# Process results
for i, result in enumerate(results):
    print(f"Text {i+1}: {result.total_annotations} annotations")
```

### Example 3: Working with Outputs
```python
result = annotator.annotate_text(your_text)

# XML output - embedded annotations
xml_content = result.xml_output
with open("annotations.xml", "w", encoding="utf-8") as f:
    f.write(xml_content)

# CSV output - structured data
csv_df = result.csv_output
csv_df.to_csv("annotations.csv", index=False)

# JSON output - ML training format
json_data = result.json_output
import json
with open("annotations.json", "w", encoding="utf-8") as f:
    json.dump(json_data, f, indent=2, ensure_ascii=False)
```

### Example 4: Error Handling
```python
# Validate before processing
is_valid, error_msg = annotator.validate_text(text)
if not is_valid:
    print(f"Invalid text: {error_msg}")
    exit()

# Process with error handling
try:
    result = annotator.annotate_text(text)
    if result.statistics.get("error"):
        print(f"Processing error: {result.statistics['error']}")
    else:
        print(f"Success: {result.total_annotations} annotations")
except Exception as e:
    print(f"Unexpected error: {e}")
```

### Example 5: Customization Options
```python
# Larger chunk sizes for better context
result = annotator.annotate_text(
    text=long_text,
    max_chunk_size=3000
)

# Different Gemini model
annotator = MedievalTextAnnotator(
    api_key="your_key",
    model_name="gemini-1.5-pro"  # More powerful model
)
```

## Output Formats

### XML Format
```xml
<annotated_text>
    <metadata>
        <summary total_annotations="15" processing_time="2.34"/>
    </metadata>
    <body>
        <annotation type="Genre" subtype="sermon" start="0" end="25">
            En el nombre de Dios
        </annotation>
        <!-- More annotations -->
    </body>
</annotated_text>
```

### CSV Format
```python
# DataFrame columns:
# - chunk_index: int
# - annotation_text: str
# - start_position: int
# - end_position: int
# - type: str (Genre, Rhetoric, Lexis, etc.)
# - subtype: str
# - text_length: int
# - chunk_length: int
# - processing_time: float
# - confidence: float
# - source: str
```

### JSON Format
```json
{
    "metadata": {
        "total_annotations": 15,
        "processing_time": 2.34,
        "chunk_count": 3
    },
    "annotations": [
        {
            "text": "En el nombre de Dios",
            "type": "Genre",
            "subtype": "sermon",
            "start": 0,
            "end": 25,
            "chunk_index": 0
        }
    ]
}
```

## Annotation Taxonomy

### Available Categories
- **Genre**: sermon, vision, consolatory_treatise, vita, confession, prayer_devotional, etc.
- **Rhetoric**: captatio, colloquialism, pathos, logos, ethos, allegory, etc.
- **Lexis**: place, person, name, gender, building, family, authority, etc.
- **Verb_Functions**: affirm, negate, question, exhort, narrate, describe, etc.
- **Notes**: hkbtext, hkbobs, intertext, classical_sources, biblical_sources, etc.

### View Full Taxonomy
```python
taxonomy = annotator.get_taxonomy()
for category, subtypes in taxonomy.items():
    print(f"{category}: {', '.join(subtypes)}")
```

## Performance Tips

### Optimal Chunk Sizes
- Small texts (< 1000 chars): Use default (1500)
- Medium texts (1000-5000 chars): Use 2000-3000
- Large texts (> 5000 chars): Use 3000-4000

### Rate Limiting
- The system automatically handles rate limiting
- Batch processing includes delays between requests
- For high-volume usage, consider upgrading Gemini API plan

### Memory Usage
- Large texts are processed in chunks
- CSV output uses pandas DataFrame (memory efficient)
- JSON output is stored in memory as dictionary

## Troubleshooting

### Common Issues
```python
# Empty results
if result.total_annotations == 0:
    print("No annotations found - check text language and content")

# API errors
if result.statistics.get("error"):
    print(f"API Error: {result.statistics['error']}")

# Text validation
is_valid, msg = annotator.validate_text(text)
if not is_valid:
    print(f"Text validation failed: {msg}")
```

### Text Requirements
- Minimum length: 10 characters
- Maximum length: 100,000 characters
- Languages: Spanish or Catalan
- Content: Medieval/historical texts work best

## Integration Examples

### Flask Web Service
```python
from flask import Flask, request, jsonify
from medieval_annotator import MedievalTextAnnotator

app = Flask(__name__)
annotator = MedievalTextAnnotator(api_key="your_key")

@app.route('/annotate', methods=['POST'])
def annotate():
    text = request.json.get('text')
    result = annotator.annotate_text(text)
    return jsonify({
        'annotations': result.json_output,
        'total': result.total_annotations
    })
```

### Command Line Tool
```python
import sys
from medieval_annotator import MedievalTextAnnotator

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python annotate.py <api_key> <text_file>")
        sys.exit(1)
    
    api_key = sys.argv[1]
    text_file = sys.argv[2]
    
    with open(text_file, 'r', encoding='utf-8') as f:
        text = f.read()
    
    annotator = MedievalTextAnnotator(api_key=api_key)
    result = annotator.annotate_text(text)
    
    print(f"Annotations: {result.total_annotations}")
    print(f"Time: {result.processing_time:.2f}s")
```
