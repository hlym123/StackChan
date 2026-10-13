
## Build

### Fetch Dependencies

```bash
python3 ./fetch_repos.py
```

### Tool Chains

[ESP-IDF v5.5.4](https://docs.espressif.com/projects/esp-idf/en/v5.5.4/esp32s3/index.html)

### Build

```bash
idf.py build
```

### Device-side AEC

Device-side acoustic echo cancellation is enabled by default for StackChan
(`CONFIG_USE_DEVICE_AEC=y`). The ES7210 captures four physical 16-bit TDM slots;
the channel mask selects slot 0 (MIC1, voice) and slot 1 (MIC3, speaker reference)
as interleaved `MR` input to the audio front end. During duplex capture and
playback, the AW88298 uses 32-bit slots on the shared 64fs bus while playback PCM
remains 16-bit. The slot width is restored whenever either codec is reopened.

This follows the [CoreS3 AEC capture fix](https://github.com/hlym123/xiaozhi-esp32-custom/commit/f038c130827c2eccb93df7d22050c45d7d54f3cd).
Disable **Enable Device-Side AEC** in `idf.py menuconfig` for comparison; keep
server-side AEC disabled when device-side AEC is enabled.

After flashing, check wake-up and recognition in a quiet room, normal speaker
playback, and voice interruption during playback. Also repeat these checks after
the microphone test and idle audio shutdown/restart. A successful build alone
does not validate echo cancellation on hardware.

### Host-side tests

The motion coordinate helpers can be tested without ESP-IDF hardware:

```bash
cmake -S tests -B build-host-tests
cmake --build build-host-tests
ctest --test-dir build-host-tests --output-on-failure
```

### Flash

```bash
idf.py flash
```
