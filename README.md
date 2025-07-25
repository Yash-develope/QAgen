# Educational QA Generation Pipeline

A pipeline for generating educational questions and answers using Google's Gemini AI with grounding capabilities.

## Features

- **Flexible Input**: Support for JSON and YAML input files
- **Multiple Question Types**: MCQ, VSA, SA, LA, and Case Study questions
- **Grounding**: Uses Google Search for fact verification
- **Configurable**: Easy configuration via YAML
- **Logging**: Comprehensive logging with rotation
- **Batch Processing**: Process multiple input files at once

## Project Structure

```
edu-qa-agentic/
├─ config.yaml              # Configuration file
├─ requirements.txt         # Python dependencies
├─ README.md               # This file
├─ logs/                   # Log files (auto-created)
├─ input/                  # Input JSON/YAML files
│   └─ comprehensive_input.json
├─ output/                 # Generated outputs (auto-created)
└─ src/                    # Source code
    ├─ __init__.py
    ├─ main.py             # Main entry point
    ├─ config.py           # Configuration loader
    ├─ client.py           # Gemini API client
    ├─ prompts.py          # Prompt rendering
    ├─ input_output.py     # I/O handling
    └─ logs.py             # Logging setup
```

## Setup

1. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Set Environment Variables**:
   Create a `.env` file in the project root:
   ```
   GEMINI_API_KEY=your_gemini_api_key_here
   ```

3. **Configure Pipeline**:
   Edit `config.yaml` to customize settings (API key is loaded from .env):
   ```yaml
   gemini:
     model: "gemini-2.5-flash"
     temperature: 0.7
     max_output_tokens: 8192
   ```

## Usage

### Basic Usage

1. **Add Input Files**: Place JSON or YAML files in the `input/` folder
2. **Run Pipeline**: Execute the pipeline using one of these methods:
   
   **Method 1 - Using run script (recommended)**:
   ```bash
   python run.py
   ```
   
   **Method 2 - Direct execution**:
   ```bash
   python src/main.py
   ```
   
   **Method 3 - Test setup first**:
   ```bash
   python test_setup.py
   python run.py
   ```
   
3. **Check Outputs**: Generated questions will be saved in the `output/` folder

### Input File Format

Input files should be JSON or YAML with the following comprehensive structure:

```json
{
  "topic": "Chemical Reactions",           // Required
  "board": "",                            // Optional - can be empty
  "grade": null,                          // Optional - can be null
  "subject": "Chemistry",                 // Optional - can be empty
  "book": "",                             // Optional - can be empty
  "chapter": "Chemical Reactions",        // Optional - can be empty
  "question_types": [],                   // Optional - can be empty array
  "num_questions": null,                  // Optional - can be null
  "difficulty_level": "Medium",           // Optional - can be empty
  "test_type": ""                         // Optional - can be empty
}
```

**Required Fields**:
- `topic`: The main topic for question generation (cannot be empty)

**Optional Fields** (can be empty, null, or have values):
- `board`: Educational board (e.g., "CBSE", "NCERT") - empty string if not specified
- `grade`: Grade level (integer) - null if not specified
- `subject`: Subject name (e.g., "Physics", "Mathematics") - empty string if not specified
- `book`: Textbook name - empty string if not specified
- `chapter`: Chapter name - empty string if not specified
- `question_types`: List of question types ["MCQ", "VSA", "SA", "LA", "CS"] - empty array if not specified
- `num_questions`: Number of questions to generate - null if not specified
- `difficulty_level`: "Easy", "Medium", or "Hard" - empty string if not specified
- `test_type`: "Quick test", "Middle test", or "Final test" - empty string if not specified

**Note**: Empty fields will be treated as "Not specified" in the prompt and the AI will make appropriate assumptions.

### Question Types

- **MCQ**: Multiple Choice Questions (4 options, 1 correct)
- **VSA**: Very Short Answer (1-2 line answers)
- **SA**: Short Answer (2-3 sentence explanations)
- **LA**: Long Answer (detailed explanations)
- **CS**: Case Study (scenario with sub-questions)

### Test Types

- **Quick test**: 3 MCQ, 2 VSA
- **Middle test**: 3 VSA, 2 SA
- **Final test**: 3 LA, 2 CS

## Output Format

Each input file generates a corresponding output file with the following structure:

```json
{
  "input": {
    // Original input parameters
  },
  "output": {
    "questions": [
      {
        "id": "chunk_prefix_1",
        "question": {
          "question_text": "What is...",
          "question_type": "MCQ",
          "options": [
            {"option_text": "Option A", "is_correct": false},
            {"option_text": "Option B", "is_correct": true}
          ]
        },
        "answer": {
          "answer_text": "Option B"
        },
        "difficulty_level": "Medium",
        "explanation": {
          "summary": "Explanation of why this question is suitable..."
        }
      }
    ],
    "sources": [
      {
        "index": 0,
        "url": "https://example.com",
        "title": "Source Title"
      }
    ],
    "search_queries": ["search terms used"],
    "generation_time": 5.23,
    "success": true
  }
}
```

## Configuration

### config.yaml Options

```yaml
# Gemini API Configuration
gemini:
  # API key is loaded from GEMINI_API_KEY environment variable
  model: "gemini-2.5-flash"
  temperature: 0.7
  max_output_tokens: 8192

# Pipeline Configuration
pipeline:
  input_folder: "input"
  output_folder: "output"
  logs_folder: "logs"
  log_level: "INFO"
  log_file: "pipeline.log"

# Processing Configuration
processing:
  enable_grounding: true      # Enable Google Search grounding
  enable_google_search: true
  batch_size: 1              # Process one file at a time
```

## Logging

Logs are written to `logs/pipeline.log` with automatic rotation:
- Maximum file size: 10MB
- Backup files: 5
- Log levels: DEBUG, INFO, WARNING, ERROR, CRITICAL

## Error Handling

The pipeline handles various error scenarios:
- Invalid input files
- Missing required fields
- API errors
- JSON parsing errors
- File I/O errors

All errors are logged with detailed information for debugging.

## Examples

### Example 1: Basic Chemistry Questions
```json
{
  "topic": "Chemical Reactions",
  "subject": "Chemistry",
  "grade": 10,
  "difficulty_level": "Medium"
}
```

### Example 2: Mathematics with Specific Types
```json
{
  "topic": "Quadratic Equations",
  "subject": "Mathematics",
  "grade": 10,
  "question_types": ["MCQ", "LA"],
  "num_questions": 3,
  "difficulty_level": "Hard"
}
```

### Example 3: Using Test Type Preset
```json
{
  "topic": "Photosynthesis",
  "subject": "Biology",
  "grade": 9,
  "test_type": "Final test"
}
```

## Troubleshooting

1. **API Key Issues**: Ensure `GEMINI_API_KEY` is set in your `.env` file
2. **Input Validation**: Check that required fields are present and valid
3. **File Permissions**: Ensure write permissions for output and logs folders
4. **Network Issues**: Check internet connection for Google Search grounding

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the MIT License. 