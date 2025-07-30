# Move Tool Fix - Debugging Approach

## Problem
The move tool is still not working after enabling move mode - clicking anywhere doesn't move the object.

## New Approach - Comprehensive Debugging

### 1. Created MoveHandler Component
- Uses `useThree` hook to access Three.js internals properly
- Adds direct event listener to canvas element
- Uses proper raycasting with ground plane intersection
- Includes extensive console logging for debugging

### 2. Enhanced Test Component
- Added click counter to verify clicks are being detected
- Added position monitoring to see if objects actually move
- Added manual test button to verify updateObject function works
- Enhanced debugging information display

### 3. Simplified Canvas Structure
- Removed complex ground plane mesh approach
- Removed Canvas onClick handler that might interfere
- Clean separation of concerns

## Current Implementation

### MoveHandler.jsx
```javascript
// Direct canvas event listener approach
const handleClick = (event) => {
  if (transformMode !== 'move' || !selectedObject) return
  
  // Calculate mouse coordinates
  const mouse = new THREE.Vector2()
  mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1
  mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1
  
  // Raycast to ground plane
  raycaster.setFromCamera(mouse, camera)
  const groundPlane = new THREE.Plane(new THREE.Vector3(0, 1, 0), 0)
  
  if (raycaster.ray.intersectPlane(groundPlane, intersectionPoint)) {
    updateObject(selectedObject.id, {
      position: [intersectionPoint.x, 0.5, intersectionPoint.z]
    })
  }
}

canvas.addEventListener('click', handleClick)
```

### Enhanced Test Component
- Shows real-time debugging info
- Click counter to verify events
- Position tracking
- Manual move test button

## Debugging Steps

1. **Add Test Cube** - Creates a red cube at origin
2. **Enable Move Mode** - Sets transformMode to 'move'
3. **Click on Canvas** - Should trigger move handler
4. **Check Console** - Look for debug messages:
   - "Click detected!"
   - "Mouse coordinates: ..."
   - "Intersection found: ..."
   - "Moving object to: ..."
5. **Test Manual Move** - Bypasses click system to test updateObject

## Expected Console Output
When working correctly, you should see:
```
Click detected! {transformMode: 'move', selectedObject: true}
Mouse coordinates: Vector2 {x: 0.2, y: -0.1}
Intersection found: Vector3 {x: 1.5, y: 0, z: -0.8}
Moving object to: [1.5, 0.5, -0.8]
```

## Troubleshooting

### If No Click Events:
- Check if OrbitControls is blocking events
- Verify canvas element exists
- Check z-index issues

### If Clicks Detected But No Movement:
- Test manual move button
- Check if updateObject function works
- Verify selectedObject has valid ID

### If Raycasting Fails:
- Check camera position and orientation
- Verify raycaster setup
- Test with different ground plane

## Files Modified
- `src/three/MoveHandler.jsx` - New component with direct event handling
- `src/three/CanvasWrapper.jsx` - Integrated MoveHandler
- `src/components/MoveToolTest.jsx` - Enhanced debugging interface

## Next Steps
1. Test with the enhanced debug component
2. Check console logs to identify where the process fails
3. Use manual move button to isolate the issue
4. Adjust implementation based on findings
