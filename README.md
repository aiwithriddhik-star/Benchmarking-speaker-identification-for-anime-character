# Benchmarking-speaker-identification-for-anime-character
 Speaker identification for anime characters presents challenges beyond 
conventional speaker recognition, since voice actors deliberately alter their vocal 
characteristics to portray different personas, introducing acoustic variability and 
inter-character similarity not seen in datasets where speakers use their natural 
voice. Using the AnimeVox dataset of 19 anime characters, we compare a 
per-character Gaussian Mixture Model (GMM) baseline against a Universal 
Background Model (UBM) + i-vector pipeline, evaluating several classifiers (SVM, 
RBF-SVM, DNN, PLDA, cosine similarity, Random Forest) across i-vector 
dimensions of 50–200, and further compare the best i-vector-based classifier 
against an end-to-end convolutional neural network (CNN) trained on Mel 
spectrograms. The CNN achieved the highest overall accuracy (91.88%, F1-score 
0.92), followed by the i-vector + DNN classifier (89.70%), both substantially 
outperforming the GMM baseline (79.67%). We further analyze which characters 
and character pairs are most frequently confused, finding that shared voice-actor 
identity alone does not explain the observed misclassification patterns. 
##  Dataset 
animevox
## feature extraction
<img width="860" height="521" alt="image" src="https://github.com/user-attachments/assets/f544bf99-166b-4160-a65c-ff04d333925b" /><br>
Our vocal folds produce a glottal pulse which passes through our vocal tract, which 
acts as a resonant filter (shaped by tongue position, jaw, etc.) that boosts some 
frequencies (formants) and damps others. To identify what word a person is speaking, 
irrespective of who the speaker is, we focus on combination features such as tongue 
movement and sound transitions; for our case, we instead look for the combination of 
features that make a character’s voice unique (pitch pattern, resonance placement, 
etc.). Two characters voiced by the same voice actor can still be differentiated because 
there are performance-specific patterns (such as pitch pattern and spacing) that remain 
unique, whereas determining whether two clips were spoken by the same underlying 
person would instead rely on physiological cues such as vocal cord length. 
In both the MFCC and Mel-spectrogram pipelines, the audio is first smoothed 
using the Hanning function. The FFT evaluates the distribution of power across 
individual frequencies, decomposing the signal into N discrete frequency components 
indexed from 0 to N−1 for each frame. Mel filter banks warp linear frequencies to 
match human auditory perception by applying overlapping triangular filters, giving 
fine resolution at low frequencies and broader grouping at high frequencies. For 
MFCC, we then apply the DCT to each frame to compress the frequency curve into a 
collection of cosine waves (approximating the vocal tract envelope) and retain the top 
few coefficients, since they explain most of the frame’s patterns. For the Mel 
spectrogram, we instead plot frequency against time frame, since a CNN can operate 
directly on this image-like representation.
## classification 
1) GMM+ UBM
2) GMM+ UBM+ I VECTOR ( classification method used svm , svm rbf , dnn , plda , cosine similarity)
3) CNN
## result
<img width="726" height="441" alt="image" src="https://github.com/user-attachments/assets/da00b8c4-f240-4f79-8794-59b50983520a" />
<br>
Several trends emerge from this analysis. RBF SVM and DNN achieve their best performance at the lowest dimensionality (50) and degrade slightly as dimensionality increases, suggesting that additional dimensions introduce noise or redundancy to which these models are more sensitive. In contrast, linear SVM, cosine similarity, and PLDA all improve monotonically with dimension, suggesting that these methods benefit from additional discriminative information as long as the underlying structure remains close to linear. Random Forest shows a strikingly different pattern, degrading sharply as dimension increases — from 69.24% at dimension 50 to just 44.01% at dimension 200. Across all four dimension settings, RBF SVM, linear SVM, and DNN remain closely clustered in the 87–90% range, while Random Forest and cosine similarity are consistently the weakest performers. This indicates that classifiers capable of learning more discriminative decision boundaries are better suited to i-vector-based speaker recognition than simple similarity measures or tree-based models on this dataset.

<img width="1139" height="475" alt="image" src="https://github.com/user-attachments/assets/af0a74d0-3f9a-4e2a-a213-c4f879683341" />
<br>
CNN produced the best overall speaker recognition performance, achieving 91.88% accuracy, 0.92 precision, 0.92 recall, and a 0.92 F1-score. Among all i-vector classifiers, the DNN achieved the highest accuracy (89.70%) using 50-dimensional i-vectors, substantially outperforming the original per-character GMM baseline (79.67%) by over 12 points. One possible reason is that an independent GMM was trained for each character, increasing both computational cost and the risk of overfitting, particularly when the amount of training data per class was limited. In contrast, the i-vector framework models speaker variability in a shared low-dimensional space, resulting in better generalization.<br>
CNN + Mel spectrogram performed better overall than the SVM/DNN + MFCC pipeline, as the CNN is more flexible in choosing which information to retain than the hand-picked top coefficients used by MFCC.

##  character analysis 
Fern was frequently involved in CNN misclassifications, suggesting that its learned spectrogram representation shares acoustic characteristics with several other female speakers. This may indicate similarities in pitch range, speaking rate, or spectral envelope rather than a limitation of the classifier alone.<br>
Megumin and Emilia, as well as Madoka Kaname and Lucy, were not confused despite sharing a voice actor, suggesting the models learned speaker characteristics specific to the recorded performances instead of relying solely on voice-actor identity.<br>
Rem and Emilia formed the strongest bidirectional confusion pair in both models. The persistence of this confusion across both the spectrogram-based CNN and the i-vector-based DNN suggests that the similarity originates from the acoustic characteristics of the voices themselves rather than from the feature extraction method. Both speakers exhibit relatively soft vocal intensity, similar pitch ranges, and comparable speaking styles, making them difficult to distinguish.

## Conclusion
The proposed lightweight CNN, containing approximately 627k trainable parameters, achieved an overall accuracy of 91.88%, with a precision of 0.92, recall of 0.92, and F1-score of 0.92 on the anime speaker identification dataset. Despite its compact architecture, the model demonstrated strong discriminative capability while requiring significantly fewer parameters than many commonly used CNN backbones. For the i-vector framework, multiple classifiers were evaluated, including cosine similarity, Random Forest, PLDA, linear SVM, RBF SVM, and DNN. Among these, the DNN classifier achieved the highest accuracy of 89.70% using 50-dimensional i-vectors, indicating that a nonlinear classifier is more effective at exploiting the discriminative information contained in low-dimensional speaker embeddings than the other evaluated classifiers.





