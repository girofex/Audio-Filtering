# 🎵Audio-Filtering

This repository describes a project done for the course Digital Signal and Image Processing 2024 by UniGe.\
The task is to process audio signals and filter them with different approaches.\
Pipeline:

1. Computation of the audio's spectogram to identify the different musical elements
2. Application of a specific filter, based on the necessity

# 📸Dataset

I selected 4 different audios:

1. song with strong low components: [*Fever (Essential Elvis Version)*](https://www.youtube.com/watch?v=n0O7y8b6t6w) by Elvis Presley
2. voice with background noise: [*airplane pilot announcement*](https://www.youtube.com/watch?v=B-kyjKqZ3oY)
3. song with sparse arrangement: [*The Night We Met*](https://www.youtube.com/watch?v=WnUR3be5Ebk) by Lord Huron
4. rock song: [*Iris*](https://www.youtube.com/watch?v=NdYWuo9OFAw) by The Goo Goo Dolls

# 🪄Filters

we define different types of linear filters:
* *Low-Pass Filter (LPF)* and *High-Pass Filter (HPF)*
* *Band-Pass Filter (BPF)* and *Band-Stop Filter (BSF)*
* *Comb Filter*
* *All-Pass Filter (APF)*

To better understand the different effects of these filters, I experimented with the first 2 macro-categories.\
We can design different families of filters depending on the desired frequency behavior:
* *Butterworth*
* *Chebyshev*
* *Bessel-Thomson*
* *Elliptic/Cauer*

To apply the filter to an audio we use `scipy.signal.lfilter()`, that outputs the result of the digital filter.

## Low-Pass Filter
A LPF attenuates the frequencies higher than a cutoff frequency: the point where the transition band and passband meet.\
It can be compared to a stiff physical barrier: when music is playing in another room, we hear the lower notes, while the higher ones are attenuated: thus, it's used to remove hiss, sibilance or isolate bass tones.\
In this context, I chose to use the *Butterworth* filter: its main feature is that it has a maximally flat frequence response in the passband, rolling off towards zero in the stopband, so it doesn't have ripples.\
To use a Butterworth LPF we call the function `scipy.signal.butter()`.

## High-Pass Filter
The HPF attenuates the frequencies lower than the cutoff frequency.\
In the audio context, it's useful to isolate sources or remove environmental noise, plosives (vibrations due to the singer's breath into the microphone), handling noise.\
In this context, the most used is the *Chebyshev* filter: it has a steep roll-off and it can be of two types:
* Type I: most common, where the passband has a ripple. It's the optimal choice, as it has a sharp cutoff, while maintaining a reasonable passband shape.
* Type II: inverse filter, it doesn't roll off as fast and the ripple is in the stopband.
To implement the filter, we call the function `scipy.signal.cheby1()`.

## Band-Pass Filter
A BPF accepts frequencies inside the passband and rejects the remainings.\
It can be constructed by joining in series
* a HPF to allow components higher than a cutoff frequency
* a LPF to let through the frequencies lower than another cutoff frequency

The bandwidth of the filter is the difference between the two cutoffs, chosen with the shape factor: the ratio between two bandwidths measured at two different attenuation levels.\
A smaller shape factor means the filter’s transition from passband to stopband is sharper.\
The resulting shape of the bands is characterized by the Q-factor, which describes how narrow the main passband is relative to its center frequency: therefore, with a low-Q filter we have a wide passband.\
Since it can cut both the high and low frequencies, this filter is used to isolate certain elements of a track (voice, instrument etc.).\
In this case, the family to use is *Bessel-Thomson*: it's excellent when time response is critical, however it has the slowest roll-off, so it’s not great if you need very sharp frequency separation.\
To apply it, we use the function `scipy.signal.bessel()`.

## Band-Stop Filter
A BSF is the inverse of the BPF.\
Typically, the frequencies vary by semitones, so the width of the stopband is influenced by this.\
A common application of the BSF is the anti-hum filter, that suppresses the range 59-61 Hz, characteristic of the alternating current sound.\
In this case, we use the *Elliptic/Cauer* filter, that presents a ripple in both the passband and stopband.\
It's very efficient when you need very tight filtering, however the phase distortion and ripple make it less “transparent” for high-fidelity audio.\
The function to use is `scipy.signal.ellip()`.

## 💯Results

| Sample   | Element to suppress    | Filter                            | Quality of suppression |
|----------|------------------------|-----------------------------------|------------------------|
| Sample 1 | Snapping sound         | Butterworth LPF                   | Good                   |
| Sample 2 | Background noise       | Butterworth LPF and Chebyshev HPF | Partial                |
| Sample 3 | Voice                  | Bessel BPF                        | Good                   |
| Sample 4 | Guitar                 | Elliptic BSF                      | Mediocre               |

# 🏅Conclusions

These techniques are a good basis for filtering the biggest components of the audio, but in order to have more precise and defined filtering, it's better to use source separation tools to extract the vocal stem and then apply the filters explained above.
