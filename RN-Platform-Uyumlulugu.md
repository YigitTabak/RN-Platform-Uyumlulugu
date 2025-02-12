- ### Platforma Özel Stil Kullanımı
    * Tutarlı bir kullanıcı deneyimi için stil tanımlamalarında Platform.select kullanılmalıdır.
    ```jsx
    // kötü
	import { StyleSheet } from 'react-native';

	const styles = StyleSheet.create({
	button: {
		padding: 10,
		borderRadius: 5, // iOS ve Android'de farklı davranabilir.
	},
	});


    // iyi
	import { Platform, StyleSheet } from 'react-native';

	const styles = StyleSheet.create({
	button: {
		padding: 10,
		...Platform.select({
		ios: {
			borderRadius: 10, // iOS'te daha yuvarlak köşeler.
		},
		android: {
			borderRadius: 5, // Android'de daha az yuvarlak köşeler.
		},
		}),
	},
	});

    ```

### Varsayılan Yazı Tipleri ve Font Kullanımı
* Android ve iOS'ta varsayılan fontlar farklıdır. Her iki platformda da aynı font ailesi kullanılamaz, bu nedenle platforma özgü fontlar tercih edilmelidir.
```jsx
// kötü
const styles = StyleSheet.create({
  text: {
    fontFamily: 'Avenir',
  },
});


// iyi
const CustomText = () => (
  <Text style={styles.text}>Platform Uyumluluğu</Text>
);

const styles = StyleSheet.create({
  text: {
    fontFamily: Platform.select({
      ios: 'Avenir',
      android: 'Roboto',
    }),
    fontSize: 16,
  },
});

```

### Platforma Özel Bileşen kullanımı
* Platforma özel bileşenler kullanmak için Platform.select kullanılmalıdır; aksi takdirde, her iki platformda aynı bileşeni kullanmak uyumsuzluğa neden olabilir.
```jsx
// kötü
import { TouchableOpacity } from 'react-native';

const MyButton = () => (
  <TouchableOpacity style={{ padding: 10 }}>
    {/* iOS ve Android'de farklı davranabilir, ancak dikkate alınmamış. */}
  </TouchableOpacity>
);


// iyi
import { Platform } from 'react-native';
import { IOSButton, AndroidButton } from './components';

const MyButton = Platform.select({
  ios: () => <IOSButton />, // iOS için özel bileşen.
  android: () => <AndroidButton />, // Android için özel bileşen.
})();

export default MyButton;

```


### Geri Butonu (BackHandler) Yönetimi
* Android cihazlarda fiziksel geri butonu bulunurken, iOS cihazlarda bulunmaz. BackHandler kullanımı platforma uygun şekilde ayarlanmalıdır.
```jsx
// kötü
useEffect(() => {
  const backAction = () => {
    BackHandler.exitApp();
    return true;
  };

  BackHandler.addEventListener('hardwareBackPress', backAction);

  return () => BackHandler.removeEventListener('hardwareBackPress', backAction);
}, []);

// iyi
import { BackHandler, Platform } from 'react-native';
useEffect(() => {
if (Platform.OS === 'android') {
const backAction = () => {
Alert.alert('Dikkat!', 'Çıkmak istiyor musunuz?', [
{ text: 'Hayır', style: 'cancel' },
{ text: 'Evet', onPress: () => BackHandler.exitApp() }
]);
return true;
};
BackHandler.addEventListener('hardwareBackPress', backAction);
return () => BackHandler.removeEventListener('hardwareBackPress', backAction);
}
}, [] );
```
