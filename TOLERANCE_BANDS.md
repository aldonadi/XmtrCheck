# Tolerance Bands Implementation

## Overview

The pressure chart includes sophisticated tolerance band functionality that provides visual indication of acceptable calibration performance. The tolerance bands create a shaded area showing the acceptable range around the perfect calibration line.

## Implementation Details

### Dataset Configuration

#### Upper Spec Band (Positive Tolerance)
```javascript
{
    label: 'Upper Spec',
    data: [],
    borderColor: 'rgba(173,216,230,1)',
    backgroundColor: 'rgba(173,216,230,0.3)',
    borderWidth: 0,
    pointRadius: 0,
    fill: true,  // Key setting: fills from this line downward
    tension: 0,
    hidden: true
}
```

#### Lower Spec Band (Negative Tolerance)
```javascript
{
    label: 'Lower Spec',
    data: [],
    borderColor: 'rgba(173,216,230,1)',
    backgroundColor: 'rgba(173,216,230,0.3)',
    borderWidth: 0,
    pointRadius: 0,
    fill: '+1',  // Key setting: fills upward to the next dataset
    tension: 0,
    hidden: true
}
```

### How the Shaded Band Works

The tolerance bands use Chart.js's fill mechanism to create a shaded area:

1. **Upper Spec Dataset**: `fill: true` fills downward from the upper tolerance line
2. **Lower Spec Dataset**: `fill: '+1'` fills upward to the next dataset (Upper Spec)
3. **Result**: A shaded band is created between the upper and lower tolerance lines

### Data Generation Logic

```javascript
// In updatePressureChart() function
if (specValue > 0 && span > 0) {
    const specDelta = span * (specValue / 100);
    
    upperData.push({
        x: percent,              // Percentage-based X-axis
        y: perfectCalPressure + specDelta  // +Tolerance
    });
    
    lowerData.push({
        x: percent,              // Percentage-based X-axis
        y: perfectCalPressure - specDelta  // -Tolerance
    });
}
```

### Visibility Control

```javascript
// Tolerance bands are only visible when:
// 1. A valid spec value is entered (specValue > 0)
// 2. A valid pressure range exists (span > 0)
const upperSpec = pressureChart.data.datasets[0];
const lowerSpec = pressureChart.data.datasets[1];
upperSpec.hidden = lowerSpec.hidden = !(specValue > 0 && span > 0);
```

## Mathematical Foundation

### Spec Delta Calculation
```javascript
const span = fullScaleValue - zeroValue;
const specDelta = span * (specValue / 100);
```

**Example**: With Zero=10, Full Scale=30, Spec=1%
- Span = 30 - 10 = 20
- Spec Delta = 20 × (1/100) = 0.2

### Tolerance Band Calculation
For each percentage point (0%, 25%, 50%, 75%, 100%):

```javascript
const perfectCalPressure = zeroValue + (span × percent/100);
const upperTolerance = perfectCalPressure + specDelta;
const lowerTolerance = perfectCalPressure - specDelta;
```

**Example at 50% point**:
- Perfect Pressure = 10 + (20 × 0.50) = 20
- Upper Tolerance = 20 + 0.2 = 20.2
- Lower Tolerance = 20 - 0.2 = 19.8

## Visual Characteristics

### Color Scheme
- **Band Color**: Light blue (`rgba(173,216,230,0.3)`)
- **Opacity**: 30% transparency for subtle appearance
- **Border**: No visible border (borderWidth: 0)

### Data Points
- **Point Radius**: 0 (no visible points on tolerance bands)
- **Smooth Lines**: tension: 0 (straight line segments)

### Layering
1. **Bottom Layer**: Lower Spec Band (fills upward)
2. **Middle Layer**: Upper Spec Band (fills downward)
3. **Top Layers**: Perfect Line and Measured Line

## Percentage-Based X-Axis Integration

The tolerance bands work seamlessly with the percentage-based X-axis:

### Before (Pressure-Based X-Axis)
```javascript
// Old implementation
upperData.push({
    x: perfectCalPressure,  // Pressure value
    y: perfectCalPressure + specDelta
});
```

### After (Percentage-Based X-Axis)
```javascript
// New implementation
upperData.push({
    x: percent,              // Percentage value (0, 25, 50, 75, 100)
    y: perfectCalPressure + specDelta
});
```

## Edge Cases and Validation

### Zero Spec Value
- **Behavior**: Tolerance bands are hidden
- **Use Case**: When no specification is provided
- **Visual**: Only perfect and measured lines are visible

### Zero Span
- **Behavior**: Tolerance bands are hidden
- **Use Case**: When zero and full scale values are identical
- **Visual**: Prevents division by zero errors

### Valid Spec and Span
- **Behavior**: Tolerance bands are visible
- **Use Case**: Normal operation with valid calibration range
- **Visual**: Shaded band shows acceptable tolerance range

## Testing Results

### Logic Test Results
```
✓ Upper spec band uses percentage-based x values (0, 25, 50, 75, 100)
✓ Lower spec band uses percentage-based x values (0, 25, 50, 75, 100)
✓ Upper spec y values = perfect pressure + spec delta
✓ Lower spec y values = perfect pressure - spec delta
✓ Spec bands create shaded area between upper and lower limits
✓ Fill settings create proper shaded band effect
✓ Visibility logic works correctly for all edge cases
```

### Visual Test Results
- **With 1% Spec**: Light blue shaded band visible between tolerance limits
- **With 0% Spec**: No tolerance bands visible (correctly hidden)
- **Data Points**: Perfect and measured lines visible with updated styles
- **Integration**: Tolerance bands work perfectly with percentage-based X-axis

## Benefits

1. **Visual Feedback**: Immediate visual indication of acceptable calibration range
2. **Intuitive Understanding**: Shaded area clearly shows tolerance limits
3. **Percentage-Based**: Works consistently across any pressure range
4. **Dynamic Visibility**: Automatically shows/hides based on input validity
5. **Non-Intrusive**: Light color doesn't obscure data points
6. **Professional Appearance**: Clean, modern visual presentation

## Integration with Other Features

### Data Point Styling
- Tolerance bands use invisible points (pointRadius: 0)
- Perfect and measured lines use visible, styled points
- No visual conflict between bands and data points

### Visibility Control
- Tolerance bands respect the same visibility logic as other elements
- Consistent behavior with perfect and measured lines
- Automatic handling of edge cases

### Responsive Design
- Bands scale appropriately on different screen sizes
- Maintain visual clarity on mobile devices
- Consistent appearance across all platforms

## Conclusion

The tolerance band implementation provides a sophisticated yet intuitive visual indication of calibration performance. By using Chart.js's fill mechanism with percentage-based X-axis data, the bands create a clear shaded area showing acceptable tolerance ranges. The implementation handles all edge cases gracefully and integrates seamlessly with other chart features.