✅ IMPLEMENTED: Both Perfect Line and Measured Line plots now use the correct X axis values. The X values for both line plots are now equal to the 0%, 25%, 50%, 75%, and 100% "perfect cal" pressures, respectively. For example, if the Zero is 0 and the 100% is 100, then the Perfect Line is plotted on these data points: (x=0, y=0), (25, 25), (50, 50), (75, 75), (100, 100). If the user enters these as measured values: 0.5, 22, 54, 81, 99; then the "measured line" is plotted at these data points: (x=0, y=0.5), (25, 22), (50, 54), (75, 81), (100, 99). The X values are now always the same for both the perfect and measured lines. For the perfect line, y = x for every point (linear line). For the Measured line, y=measured value for the respective data point.

Additional fix: Added X-axis scale configuration with min and max values to ensure the chart displays the full range from Zero to Full Scale values.

Implementation details can be found in IMPLEMENTATION_SUMMARY.md
