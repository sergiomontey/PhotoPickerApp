# PhotoPickerApp
# Photo Picker & Camera App

A simple React Native (bare workflow) app that lets you pick a photo from the library or take a new photo with the camera.

## Prerequisites

- macOS with Xcode 14+ (for iOS builds)  
- Android Studio with an emulator or connected device  
- Node.js (16+), npm or Yarn  
- React Native CLI tools installed

## 1. Initialize the Project

```bash
# Create a new bare React Native app
npx react-native init PhotoPickerApp
cd PhotoPickerApp
```

## 2. Install Dependencies

```bash
# Using Yarn
yarn add react-native-image-picker

# Or with npm
npm install react-native-image-picker
```

React Native ≥ 0.60 will auto-link native modules.

## 3. iOS Setup

```bash
cd ios
pod install
cd ..
```

Edit `ios/PhotoPickerApp/Info.plist`, add:

```xml
<key>NSCameraUsageDescription</key>
<string>We need camera access to take your photo</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>We need photo library access to pick your pictures</string>
```

## 4. Android Setup

Edit `android/app/src/main/AndroidManifest.xml` inside `<manifest>`:

```xml
<uses-permission android:name="android.permission.CAMERA"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
```

## 5. Create the `PhotoPicker` Component

Create `src/components/PhotoPicker.tsx`:

```tsx
import React, {useState} from 'react';
import {View, Button, Image, StyleSheet, Alert} from 'react-native';
import {
  launchCamera,
  launchImageLibrary,
  CameraOptions,
  ImageLibraryOptions,
  Asset,
} from 'react-native-image-picker';

export default function PhotoPicker() {
  const [photo, setPhoto] = useState<Asset | null>(null);

  const opts: CameraOptions & ImageLibraryOptions = {
    mediaType: 'photo',
    quality: 0.8,
    saveToPhotos: true,
  };

  async function pickFromLibrary() {
    const res = await launchImageLibrary(opts);
    if (res.didCancel) return;
    if (res.errorCode) {
      Alert.alert('Error', res.errorMessage || 'Unknown error');
      return;
    }
    setPhoto(res.assets?.[0] ?? null);
  }

  async function takePhoto() {
    const res = await launchCamera(opts);
    if (res.didCancel) return;
    if (res.errorCode) {
      Alert.alert('Error', res.errorMessage || 'Unknown error');
      return;
    }
    setPhoto(res.assets?.[0] ?? null);
  }

  return (
    <View style={styles.container}>
      <View style={styles.buttons}>
        <Button title="Pick from Library" onPress={pickFromLibrary} />
        <Button title="Take Photo" onPress={takePhoto} />
      </View>
      {photo && (
        <Image source={{uri: photo.uri}} style={styles.preview} resizeMode="contain" />
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {flex: 1, padding: 16, backgroundColor: '#fff'},
  buttons: {flexDirection: 'row', justifyContent: 'space-around', marginBottom: 16},
  preview: {flex: 1, borderRadius: 8},
});
```

## 6. Update `App.tsx`

Replace `App.tsx` contents with:

```tsx
import React, {useState} from 'react';
import {SafeAreaView, StyleSheet, Button, View} from 'react-native';
import PhotoPicker from './src/components/PhotoPicker';

export default function App() {
  const [showPicker, setShowPicker] = useState(false);

  return (
    <SafeAreaView style={styles.safe}>
      <View style={styles.header}>
        <Button
          title={showPicker ? 'Back' : 'Photo Picker'}
          onPress={() => setShowPicker(!showPicker)}
        />
      </View>
      {showPicker ? <PhotoPicker /> : null}
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safe: {flex: 1},
  header: {padding: 16},
});
```

## 7. Run the App

```bash
# iOS
yarn ios --simulator="iPhone 11"

# Android
yarn android
```

Tap **“Photo Picker”**, then **“Pick from Library”** or **“Take Photo”**!

## 8. Commit & Push to GitHub

```bash
git init
git add .
git commit -m "Initial commit: Photo Picker & Camera App"
# replace with your GitHub URL
git remote add origin git@github.com:<username>/PhotoPickerApp.git
git push -u origin main
```

---
