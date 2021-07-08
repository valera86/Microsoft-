The "debug tap" is a special mode that lets you see exactly which characters are being sent to the terminal, and exactly what input the Terminal is sending to the client application. This is particularly helpful for debugging rendering and input issues. 

![image](https://user-images.githubusercontent.com/18356694/99067818-f1e7d780-2570-11eb-9790-3e6cce8a96be.png)

To enable the debug tap:

1. Enable debug features by adding `"debugFeatures": true,` to the global settings
2. Hold both the left and right <kbd>alt</kbd> keys, and open a new tab. What you should see is a tab with two panes in it, and the second one will have the "debug tap", showing all the input and output sent to the Terminal.

At this point, you should repro your issue, and be able to see everything that's in/output to the Terminal. If you've been asked to use the debug tap to repro your issue, please copy the output in the second pane to your reply. If your issue has anything to do with input, then maybe also share a screenshot, so we can differentiate between the output and input (the red text).