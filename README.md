```C
#include <Arduino.h>
#include <hal/hal_afe.h>

static void tone_init(void)
{
    static int once = 0;
    if (0 == once)
    {
        AFE_Chip_Init();
        AFE_TurnOnAudioClock(ASP_FS_8K, DL_PATH);
        AFE_SwitchHPon();
        //AFE_Switch_Amp(true);
        once = 1;
    }
}

void noTone(uint8_t _pin)
{
    AFE_DAC_TEST = 0;
}

/* 31 tones, step125,  125-3875Hz */
void tone(uint8_t _pin, unsigned int frequency, unsigned long duration)
{
    tone_init();
    // Audio frequency = Sampling rate / 64 * FREQ_DIV
    AFE_DAC_TEST = ADAC_SINUS | AMP_DIV(7) | FREQ_DIV((frequency * 64) / 8000);
    if (duration)
    {
        delay(duration);
        noTone(0);
    }
}

void beep(char f)
{
    tone_init();
    AFE_DAC_TEST = ADAC_SINUS | AMP_DIV(7) | FREQ_DIV(f);
    delay(100);
    AFE_DAC_TEST = 0;
}
```
