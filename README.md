# wasapi_barebones
A blank Unity project with the plugin for CS Core audio library and WASAPI

Features 
- Spectral Analysis (FFT of audio waveform)
- Time Averaging/Smoothing
- Beat Mapping per frequency 
- Audio Buffer

![](wasapi_spectrum.gif)


## Creating a 2D Texture from the Audio Buffer
```C#
    public LoopbackAudio Audio;
    public int bufferSmooth = 0;
    public Gradient gradient; 
    VisualEffect visualEffect;
    Texture2D texture;

    
    public Texture2D CreateTexture(float[,] data, int width, int height)
    {
        texture = new Texture2D(height, width, TextureFormat.ARGB32, false);
        texture.filterMode = FilterMode.Point;

        for (int i = 0; i < height; i++)
        {
            for (int j = 0; j < width; j++)
            {
                    texture.SetPixel(width - 1 - j, height - 1 - i, gradient.Evaluate(data[i,j]/10f));
            }
        }
        texture.Apply();
        return texture;
    }
```
Call the function like such
```C#
float[,] data = new float[Audio.audioQueue.Count, Audio.SpectrumSize];
data = Audio.getBuffer(bufferSmooth);
visualEffect.SetTexture("AudioTexture", CreateTexture(data, Audio.audioQueue.Count, Audio.SpectrumSize)  );     
```
The audio buffer will automatically save data from the WeightedPostScaledSpectrum, which is the time averaged spectrum. The `bufferSmooth` parameter will smooth data in time before returning it. This will round out sharp peaks in the spectrogram
