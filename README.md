# Brain-to-text
Neuro-Phonetic Multi-Scale Transformer (NP-MST)

By Michael K Rapoport, Engineer


Summary of the Invention
The present invention generally relates to Computational Neuroscience / Adaptive Signal Processing, and more specifically to a system and method as defined in the claims.

Detailed Description
This competition focuses on developing novel algorithms to decode speech directly from brain activity. Participants are provided with a dataset of neural spiking activity recorded from a research participant attempting to speak. The challenge is to create a model that can accurately predict the words being spoken from this neural data. 

System Requirements and Environment Setup
To implement the Neuro-Phonetic Multi-Scale Transformer (NP-MST), the development environment requires Python 3.10 or higher. Essential libraries include PyTorch 2.1+, NumPy, SciPy for signal processing, and the Hugging Face 'transformers' and 'tokenizers' libraries for linguistic modeling. Given the high dimensionality of neural spiking data (often 256+ channels at 1ms resolution), a GPU with at least 24GB VRAM (e.g., NVIDIA RTX 4090 or A100) is necessary to handle large batch sizes and windowed temporal convolutions.

Data Preprocessing and Feature Engineering
The raw neural data consists of spike counts per millisecond across multiple electrode arrays. The implementation begins with a temporal binning process, aggregating spikes into 20ms windows to reduce noise while maintaining phonetic resolution. These binned counts undergo a square-root transformation to stabilize variance, followed by z-score normalization based on the mean and standard deviation of the "quiet" baseline periods. To account for neural drift, a rolling mean subtraction is applied across the session. The data is then augmented using 'TimeWarping' and 'Gaussian Spike Dropout,' where 5% of random channels are masked during training to force the model to learn redundant neural representations of speech features.

Architecture: The Neuro-Phonetic Multi-Scale Transformer (NP-MST)
The core innovation is a two-stage architecture. Stage one is the Spatio-Temporal Feature Extractor, utilizing three parallel 1D-convolutional layers with varying kernel sizes (3, 5, and 7) to capture multi-scale temporal dependencies of neural firing patterns. The output is concatenated and fed into a 6-layer Transformer Encoder with 8 heads and a hidden dimension of 512. Stage two is the Phonetic Alignment Decoder. Unlike standard seq2seq models, this utilizes a Connectionist Temporal Classification (CTC) head in parallel with a Cross-Attention Decoder. The CTC head predicts phoneme probabilities per time step, providing a strong inductive bias for the Transformer Decoder to focus on the chronological order of speech attempts.

Phonetic Prior Distillation and Vocabulary Mapping
To bridge the gap between brain signals and text, the model utilizes a Phonetic Prior. Instead of predicting characters directly, the model is trained to predict International Phonetic Alphabet (IPA) tokens. This reduces the ambiguity caused by silent letters and English orthography. A custom tokenizer is built using the CMU Pronouncing Dictionary to map neural spikes to phonemes, and subsequently, a second-stage light-weight Transformer (the 'Grapheme Mapper') converts phoneme sequences into standard English text. This decoupling ensures that the brain-to-text translation remains robust even if the participant's physical articulation is impaired.

Training Protocol and Loss Functions
The model is trained using a hybrid loss function: L = λ1(L_CTC) + λ2(L_CE), where L_CTC is the Connectionist Temporal Classification loss for phonetic alignment and L_CE is the Label-Smoothed Cross-Entropy loss for the final text output. A 'warm-up' scheduler is implemented for the first 1,000 steps, followed by a cosine decay learning rate starting at 1e-4. To prevent overfitting on the limited research participant data, we employ 'Stochastic Depth' in the Transformer layers and use a curriculum learning strategy—training initially on single words before progressing to full sentences.

Inference and Language Model Rescoring
During inference, the model generates candidate text sequences using Beam Search with a width of 20. To ensure grammatical and semantic coherence, the beam search is integrated with a 5-gram KenLM language model trained on the LibriSpeech corpus. The final output is selected by maximizing the joint probability: Score = log(P_neural) + α log(P_LM) + β(Word_Count), where α and β are hyperparameters tuned on a validation set. This post-processing step corrects phonetic misinterpretations (e.g., "know" vs. "no") by leveraging the linguistic context of the decoded phrase.

System Requirements and Environment Setup
To implement the Neuro-Phonetic Multi-Scale Transformer (NP-MST), the development environment requires Python 3.10 or higher. Essential libraries include PyTorch 2.1+, NumPy, SciPy for signal processing, and the Hugging Face 'transformers' and 'tokenizers' libraries for linguistic modeling. Given the high dimensionality of neural spiking data (often 256+ channels at 1ms resolution), a GPU with at least 24GB VRAM (e.g., NVIDIA RTX 4090 or A100) is necessary to handle large batch sizes and windowed temporal convolutions.

Data Preprocessing and Feature Engineering
The raw neural data consists of spike counts per millisecond across multiple electrode arrays. The implementation begins with a temporal binning process, aggregating spikes into 20ms windows to reduce noise while maintaining phonetic resolution. These binned counts undergo a square-root transformation to stabilize variance, followed by z-score normalization based on the mean and standard deviation of the "quiet" baseline periods. To account for neural drift, a rolling mean subtraction is applied across the session. The data is then augmented using 'TimeWarping' and 'Gaussian Spike Dropout,' where 5% of random channels are masked during training to force the model to learn redundant neural representations of speech features.

Architecture: The Neuro-Phonetic Multi-Scale Transformer (NP-MST)
The core innovation is a two-stage architecture. Stage one is the Spatio-Temporal Feature Extractor, utilizing three parallel 1D-convolutional layers with varying kernel sizes (3, 5, and 7) to capture multi-scale temporal dependencies of neural firing patterns. The output is concatenated and fed into a 6-layer Transformer Encoder with 8 heads and a hidden dimension of 512. Stage two is the Phonetic Alignment Decoder. Unlike standard seq2seq models, this utilizes a Connectionist Temporal Classification (CTC) head in parallel with a Cross-Attention Decoder. The CTC head predicts phoneme probabilities per time step, providing a strong inductive bias for the Transformer Decoder to focus on the chronological order of speech attempts.

Phonetic Prior Distillation and Vocabulary Mapping
To bridge the gap between brain signals and text, the model utilizes a Phonetic Prior. Instead of predicting characters directly, the model is trained to predict International Phonetic Alphabet (IPA) tokens. This reduces the ambiguity caused by silent letters and English orthography. A custom tokenizer is built using the CMU Pronouncing Dictionary to map neural spikes to phonemes, and subsequently, a second-stage light-weight Transformer (the 'Grapheme Mapper') converts phoneme sequences into standard English text. This decoupling ensures that the brain-to-text translation remains robust even if the participant's physical articulation is impaired.

Training Protocol and Loss Functions
The model is trained using a hybrid loss function: L = λ1(L_CTC) + λ2(L_CE), where L_CTC is the Connectionist Temporal Classification loss for phonetic alignment and L_CE is the Label-Smoothed Cross-Entropy loss for the final text output. A 'warm-up' scheduler is implemented for the first 1,000 steps, followed by a cosine decay learning rate starting at 1e-4. To prevent overfitting on the limited research participant data, we employ 'Stochastic Depth' in the Transformer layers and use a curriculum learning strategy—training initially on single words before progressing to full sentences.

Inference and Language Model Rescoring
During inference, the model generates candidate text sequences using Beam Search with a width of 20. To ensure grammatical and semantic coherence, the beam search is integrated with a 5-gram KenLM language model trained on the LibriSpeech corpus. The final output is selected by maximizing the joint probability: Score = log(P_neural) + α log(P_LM) + β(Word_Count), where α and β are hyperparameters tuned on a validation set. This post-processing step corrects phonetic misinterpretations (e.g., "know" vs. "no") by leveraging the linguistic context of the decoded phrase.

Claims
A system for decoding neural signals into text, the system comprising: a neural input module configured to capture spiking data from a plurality of neural channels; a temporal-spatial featurizer configured to project the spiking data into a neural manifold using a set of adaptive projection parameters; an alignment engine configured to generate phonemic sequences from the neural manifold via a cross-modal attention mechanism; a linguistic synthesis module configured to transform the phonemic sequences into a text sequence based on a language model; and a recursive adaptation engine configured to calculate a linguistic confidence metric for the text sequence and dynamically update the adaptive projection parameters of the temporal-spatial featurizer in real-time during a decoding session to minimize the divergence between the neural manifold and the linguistic synthesis output.

Conclusion
While the above description contains many specificities, these should not be construed as limitations on the scope of the invention, but rather as an exemplification of one preferred embodiment thereof.
