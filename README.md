# Grammar_scorer

**Task: Grammatical Quality Prediction**

Our goal is to accurately assess the grammatical quality of spoken language. To achieve this, we've designed a multimodal pipeline that leverages both textual and audio features.

**Pipeline Stages:**

**Audio to Text Conversion (Whisper):**

The initial stage involves transcribing the audio input using OpenAI's Whisper model. This provides us with a textual representation of the spoken language.
It is noted that the quality of these transcripts can vary significantly, with some containing multiple languages, minimal coherent text, or considerable noise.

**Text Preprocessing:**

The transcribed text undergoes preprocessing to enhance its quality. This includes:
* Language detection to identify if it is English.
* Regular expression cleaning to remove unwanted characters and noise.
* spaCy-based normalization to standardize text (e.g., lemmatization, tokenization).
* The transcribed text undergoes cleaning and normalization. Crucially, if the preprocessing reveals the text is not up to the mark—for example, it's not in English, contains very little information, or has excessive noise—the system returns an empty text output, recognizing that text features will be unreliable.

**Feature Extraction:**

*Text Features (RoBERTa):*

For high-quality text, we extract features using a pretrained RoBERTa model.
To balance performance and computational efficiency, we fine-tune only the last two layers of the RoBERTa encoder.

*Audio Features (Whisper):*

* Regardless of text quality, we extract audio features using the Whisper model.
* Audio features enhance grammar prediction by capturing intonation, pronunciation, and voice quality, providing context and robustness beyond text. They are especially valuable when text quality is poor.
*Feature Combination:*
If the text is of good quality, the text and audio embeddings are concatenated.
if the text is of bad quality, only the audio embeddings are used.


**Model Training and Validation (HybridGrammarScorer):**

* The combined (or audio-only) features are fed into our hybrid model, HybridGrammarScorer, which consists of several fully connected layers.
* The model is trained to predict a regression score representing grammatical quality.
* The dataset is split into 80% training data and 20% validation data.
* The model is trained using Mean Squared Error (MSE) loss and the Adam optimizer.
* The model's performance is evaluated using metrics like Pearson correlation, MSE, MAE, and R².
* The model with the best validation Pearson correlation is selected and saved.
**Test Set Prediction:**

* For the test set, we apply the same preprocessing and feature extraction steps.
* The trained HybridGrammarScorer model generates predictions.
* The final predictions are saved into a submission file.
