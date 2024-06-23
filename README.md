# Butter Noise Fliter 奶油聲音過濾器

## Introduction 簡介
This repository provides a simple example and demonstration of a Butterworth filter in Python, particularly for audio with a complicated background. To avoid environment issues, the main body of code was built on Google Colab, allowing it to run smoothly without any pre-construction.

本專案提供了一個用 Python 實現的 Butterworth 濾波器的簡單示例和演示，特別適用於處理背景複雜的音頻。為了使程式碼運行更加簡單，所有的內容將在 Google Colab 上實現，所以不需要做環境上的調整。

## Result 成果展示
Input: YouTube URL (or apply Default) Output: org_audio.mpe(original audio) processed_audio.mp3（processed audio）

輸入：YouTube 網址（亦可以使用 Default）輸出：org_audio.mpe（紀錄原始音訊）processed_audio.mp3（處理後的音訊）

<img src="https://github.com/E84081210/Test/blob/main/Result/5.0_result.png" alt="專案封面圖" width="80%">

Top image: waveform of the original audio. Bottom image: waveform of the processed audio. The audio quality shows noticeable differences, especially in the reduced background noise and the highlighted vocals. However, the processed audio sounds noticeably thinner.

圖片上方：原始音訊的波形圖。圖片下方：處理後的音訊波形圖。音訊的品質有很明顯的差異，特別是在背景噪音降低和人聲凸顯的部分，不過，處理後的音訊明顯比較單薄。

## Iteration 版本迭代

### Version :one: 第一版
In version I, an arbitrary audio signal was used for preliminary algorithm testing. However, the results were relatively unsatisfactory, with significant degradation in audio quality.

在版本 I 中，我使用了隨機音頻信號進行初步的算法測試。然而，結果非常不理解，音質顯著地下降了。

<img src="https://github.com/E84081210/Test/blob/main/Result/preliminary_test.png" alt="第一次測試" width="80%">

```python
# Generate simulated data
np.random.seed(0)
fs = 8000  # Sampling rate
t = np.linspace(0, 1, fs, endpoint=False)
desired_signal = np.sin(2 * np.pi * 300 * t)  # Simulated desired audio signal
noise = 0.5 * np.random.normal(size=t.shape)  # Simulated noise
received_signal = desired_signal + noise  # Signal received by microphone

# LMS filter implementation
def lms_filter(received_signal, desired_signal, mu, num_taps):
    n = len(received_signal)
    h = np.zeros(num_taps)  # Filter weights initialization
    output = np.zeros(n)  # Output signal
    error = np.zeros(n)  # Error signal
    
    for i in range(num_taps, n):
        x = received_signal[i:i-num_taps:-1]
        y = np.dot(h, x)
        error[i] = desired_signal[i] - y
        h = h + 2 * mu * error[i] * x
        output[i] = y
    
    return output, error, h

# Set LMS filter parameters
mu = 0.01  # Learning rate
num_taps = 32  # Number of filter coefficients

# Apply LMS filter
output_signal, error_signal, filter_weights = lms_filter(received_signal, desired_signal, mu, num_taps)
```
### Version :two: - :four: 第二 - 第四版
Starting from version II, I introduced real videos for testing, primarily including the following three types:

- Videos with slight background noise, such as official live MV recordings.
- Edited daily life videos, such as Vlogs produced by professional YouTubers.
- Unedited daily life videos, such as those shot with smartphones and directly uploaded after minimal editing.

The first two types underwent advanced filtering that preserved some noise, making further filtering unnecessary. However, the filtered audio resulted in many flaws and even introduced additional noise. This extra noise is not actual erroneous signals but rather auditory illusions perceived by humans, making the sound appear muffled.

Later on, I learned about low-pass and high-pass filters, including their suitable applications and the sequence in which they should be applied.

Interestingly, during testing, I found that some videos deliberately retain certain background noise to enhance realism. Therefore, after confirming the use of Butterworth filters in the algorithm, I decided to manually add some reverberation to maintain the authenticity and richness of the filtered sound.

從第二版開始，我導入了真實的影片進行測試，主要包括以下三種類型：

- 些許雜音的影片，例如：由官方發布的 Live MV 現場影片。
- 日常生活短片（有後製），例如：專業 YouTuber 拍攝的 Vlog。
- 日常生活短片（無後製），例如：以手機拍攝、經剪輯後直接上傳的 Vlog。

前兩者已經過高級濾波器處理，保留了一些雜音，因此不需要進一步過濾。然而，過濾後的音訊卻造成了許多瑕疵，甚至添加了更多的雜音。這些額外的雜音並非真實添加的錯誤訊號，而是人類聽覺上的錯覺，導致聲音聽起來更混濁。

後來，我學習到了低通濾波器和高通濾波器，以及它們適用的不同場景和使用順序。

有趣的是，在測試過程中，我發現部分影片會故意保留一些雜音，以增強真實感。因此，在確定演算法使用 Butterworth 濾波器後，我決定手動添加一些聲音的回音，以保持過濾後聲音的真實性和豐富度。

### Conclusive Version 5️⃣: 最終第五版
In the final [audio file](https://github.com/E84081210/Test/blob/main/Result/5.0_audio.mp3), I applied a Butterworth filter, added reverb effects, and performed normalization at several stages.

在最終的[音訊檔案](https://github.com/E84081210/Test/blob/main/Result/5.0_audio.mp3)中，我套用了 Butterworth Filter 以及添加 Reverb 回迴，並在許多地方做了正規化的處理。

## Retrospective 檢討
In fact, after initial adjustments to my algorithm design, I achieved relatively stable waveforms. However, the audio always had an indescribable strangeness – voices became very thin, distorted, almost surreal. At first listen, one might think the recording was made in a bathtub or using an old-fashioned phone. While I did achieve my goals from a data perspective, I further optimized for realism in human perception. I preserved waveform integrity while making voices more prominent and reducing surrounding noise.

事實上，在我最初的演算法設計調整後，我就能得到相對穩定的波形圖，但音訊總是有一種難以言喻的怪異感－聲音變得十分單薄、扭曲、超現實。乍聽之下，會以為這個人是在浴缸裡面錄製、或是使用老款的手機撥打電話。從數據上，我確實達到了目標，但對於人的聽感而言，我還做了更多真實性的優化，在保留波形完整的情況下，使得人聲更加凸顯、周遭的雜音下降。

Development process 開發流程 <img src="https://github.com/E84081210/Test/blob/main/dev_log.PNG" alt="開發流程" width="80%">

