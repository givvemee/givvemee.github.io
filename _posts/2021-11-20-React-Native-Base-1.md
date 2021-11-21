---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: React_Native

---

### React Native! Base 01

노마드코더와 함께 하는 React Native. 

#### Preparation

휴대폰에 Expo 어플을 설치해 준다.

![image](https://user-images.githubusercontent.com/89691274/142723670-d0d53dcd-a102-4c23-893c-95335684e0c0.png)

Compile 을 하여 앱을 만드는 게 아니라 Expo 를 이용하여 컴퓨터에서 작성한 코드를 Expo 앱을 이용하여 미리보기 할 수 있다.

`node -v` 

`npm install --global expo-cli`

`brew install watchman`

여기까지 하면 React Native 아주 아주 기본 준비는 완료.

_참고 인포_

https://snack.expo.dev/  : 브라우저에서 React 앱을 만들 수 있게 해 주는 온라인 에디터! 이것을 통해 바로 React App 을 만들 수 있다.

<br/>

<br/>

#### 개념 이해

React.js 웹 페이지를 만들 때는 코드를 바로 바로 작성해도 되지만 React Native 는 모바일 어플리케이션이므로 브라우저가 아니다. 

![image](https://user-images.githubusercontent.com/89691274/142723765-67d729ac-ebe7-4551-87ad-119a3a0c9914.png)

화면에서 버튼을 누르는 이벤트가 있다고 하자. (1) 그러면 Native 는 해당 이벤트를 감지한다. 그리고 화면 어디에서 이 이벤트가 왜, 어디서, 어떻게 발생했는지 정보를 수집한다. (2) 그러면 Native 는 해당 정보를 가지고 JSON 메시지를 생성한다. (3) 그리고 Javascript 코드는 해당 메시지를 받는다. (4) 코드를 실행시키면 Native 에 다시 전달해 준다. 사진 상단의 Javascipt 는 여기서 메시지를 받고 보내는 역할을 한다. 개발자들이 메시지를 주고 받기 위해 쓰는 레이어일 뿐. 

한 마디로, 이벤트가 생기면 bridge 에서 그걸 받아와서 Javascript 코드로 실행한 다음 다시 Native 로 보낸다!

<br/>

<br/>

#### 프로젝트 생성

![image](https://user-images.githubusercontent.com/89691274/142724008-6d950f03-560d-4555-b61b-d8590f953ebf.png)

`expo init 파일명` 으로 리액트 네이티브 앱을 만들 디렉터리를 생성해 준다. 선택지에서는 첫번째 옵션을 선택 하고 코드를 켜서

`yarn start` 를 입력하면 큐알 코드가 나온다.

![image](https://user-images.githubusercontent.com/89691274/142724189-6a6ffcf9-1d77-4906-96ab-e8dfa4a0b849.png)

`expo login` 명령어를 통해서 Expo 앱에서 가입한 유저네임과 비밀번호를 입력한당. 그런 다음 다시 `yarn start`!

그리고 나서 휴대폰에 설치한 Expo 앱을 켜서 목록에서 현재 구동되고 있는 나의 서버를 클릭하면 

![IMG_5529](https://user-images.githubusercontent.com/89691274/142729339-d34a1ad3-b2d7-4034-af5f-8a6491bc8f16.PNG)

이렇게 나온당. App.js 의 텍스트 부분을 조금 수정해 주었다.

![IMG_5530](https://user-images.githubusercontent.com/89691274/142729361-5552a341-efb3-4ef2-8305-f574d48ff389.PNG)

<br/>

<br/>

#### 간단하고 빠르게 알아보는 React Native 의 규칙

- React Native 는 웹사이트가 아니므로 div 를 쓸 수 없다. 대신 View 라는 것을 사용함. 항상 import 해야 한다

```react
 import { StyleSheet, Text, View } from 'react-native';
```

- React Natvie 에 있는 text 는 text component 안에 들어가야 한다. p 태그 등은 사용할 수 없다.

  ```react
  <Text>이런 식으로</Text>
  ```

- 웹에서 쓰던 일부 style 을 사용할 수 없음 (border 등)

- `StyleSheet.create` 는 object 를 생성 그냥 Object 로만 만들어도 잘 작동하긴 한다. StyleSheet 가 꼭 필요하지는 않음. Text 에 이렇게 바로 줄 수도 있음  

  ```react
  <Text style={{fontSize: 30}}>oh my gosh</Text>
  ```

- Status bar 란? 시계 배터리 와이파이가 있는 바를 의미한다.

  ```react
   import { StatusBar } from 'expo-status-bar';
  ```

<br/>

<br/>

#### 가져와 쓸 수 있는 것들.

리액트 네이티브는 기본적으로 아주 다양한 종류의 컴포넌트와 API 를 제공한다. **https://reactnative.dev/docs/components-and-apis**

초기에는 지금보다 더 많은 컴포넌트와 API 를 제공했지만, 이제 몇몇은 사람들이 만든 앱이 올라오는 커뮤니티에 의존한다. 그러나 커뮤니티는 개인이 만드는 것이기 때문에 버그가 있어도 언제 고쳐질지 모른다는 단점이 있다. 그렇기 때문에 불안정한 커뮤니티 보다든 React Native 에서 제공하지 않는 패키지들은 Expo SDK 패키지를 사용하면 된당.

**https://docs.expo.dev/versions/latest/**

이번에 진행하는 프로젝트 또한 여기서 몇 개를 가져와 사용해 볼 것이다.

<br/>

<br/>

#### Layout 에 관한 Flex 의 이해 

```react
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { View } from 'react-native';

export default function App() {
  return (
    <View>
      <View style={{width: 100, height: 100, backgroundColor: 'tomato'}}></View>
      <View style={{width: 100, height: 100, backgroundColor: 'blue'}}></View>
      <View style={{width: 100, height: 100, backgroundColor: 'orange'}}></View>
    </View>
  );
}
```

여기서 보면 이미 View Container 자체가 Flex container 이다. **기본적으로 모든 View 는 Flex Container. 그리고 flexDirection 의 기본값을 Column 으로 가짐.** 

![IMG_5534](https://user-images.githubusercontent.com/89691274/142729506-5b96379e-1547-430d-8768-f2ccab629c59.PNG)

99.7% 의 경우에는 높이와 너비에 기반해서 만들지 않는다. 왜냐하면 스크린 사이즈에 따라 다르게 보이니까. 많은 스크린에서 동일한 방식으로 보이는 것에 대해 고민해야 한다. width 나 height 는 거의 사용하지 않을 것. 대신에 flexSize 를 사용한다.

이렇게 작성할 수 있다.

```react
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { View } from 'react-native';

export default function App() {
  return (
    <View style={{flex:1}}>
      <View style={{flex: 1, backgroundColor: 'tomato'}}></View>
      <View style={{flex: 1, backgroundColor: 'blue'}}></View>
      <View style={{flex: 1, backgroundColor: 'orange'}}></View>
    </View>
  );
}
```

![IMG_5535](https://user-images.githubusercontent.com/89691274/142729517-6fb20f44-1775-45cf-bc18-34d419ae1edd.PNG)

이런 식으로 비율을 조정하여 layout 을 만들 것임을 기억하자. 부모에 있는 flex 값이 중요하다. 그래야 자식들이 그 값을 받아 비율로 표현될 수 있기 때문에.

<br/>

<br/>

#### 막간 단축키

터미널에 R 키 입력 : Expo 앱 새로고침

터미널에 D 키 입력 : 개발자 도구 확인 가능

터미널에 M 키 입력 :  토글 메뉴 (핸드폰을 흔들기만 해도 된다)

<br/>

<br/>

#### 코드 짜기

**스타일**

지금 만들어 볼 것은 날씨 앱이다. 위치와 현재 위치의 날씨를 가져오는 작업을 하기 전에 스타일링을 미리 해 줄 것이다.

```react
import { StatusBar } from 'expo-status-bar';
import React, { useEffect, useState } from 'react';
import { View, StyleSheet, Text, ScrollView, Dimensions } from 'react-native';

const {width: screenWidth} = Dimensions.get('window');

export default function App() {
  return (
    <View style={styles.conatiner}>
      <StatusBar style="dark"/>
      <View style={styles.city}>
        <Text style={styles.cityName}>Incheon</Text>
      </View>
      <ScrollView pagingEnabled horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={styles.weather} >
        <View style={styles.day}>
          <Text style={styles.temp}>28</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>

        <View style={styles.day}>
          <Text style={styles.temp}>27</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>
        <View style={styles.day}>
          <Text style={styles.temp}>27</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  conatiner: {
    flex:1,
    backgroundColor:'#b7e69c'
  },
  city: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center'
  }, 
  cityName: {
    color: 'black',
    fontSize: 38,
    fontWeight: '600'
  },  
  weather: {
  },
  day: {
    width: screenWidth,
    alignItems: 'center'
  },
  temp: {
    fontSize: 180,
    marginTop: 50,
    fontWeight: '600'
  },
  desc: {
    fontSize: 44,
    marginTop: -20
  }
})
```

<br/>

**위치 설정**

`expo install expo-loaction` 명령어를 이용하여 먼저 위치를 사용할 것임을 생각하자.

https://docs.expo.dev/versions/v43.0.0/sdk/location/

```react
import { StatusBar } from 'expo-status-bar';
import * as Location from 'expo-location';
import React, { useEffect, useState } from 'react';
import { View, StyleSheet, Text, ScrollView, Dimensions } from 'react-native';
const {width: screenWidth} = Dimensions.get('window');


export default function App() {
  const [region, setRegion] = useState('loading...')
  const [location, setLocation] = useState()
  const [ok, setOk] = useState(true)
  const ask = async () => {
    const {granted} = await Location.requestForegroundPermissionsAsync()
    if (!granted) {
      setOk(false)
    }
    const {coords: {latitude, longitude}} = await Location.getCurrentPositionAsync({accuracy: 5})
    const location = await Location.reverseGeocodeAsync({latitude, longitude}, {useGoogleMaps: false})
    setRegion(location[0].region)
  }
  useEffect(() => {
    ask()
  }, [])
  return (
    <View style={styles.conatiner}>
      <StatusBar style="dark"/>
      <View style={styles.city}>
        <Text style={styles.cityName}>{region}</Text>
      </View>
      <ScrollView pagingEnabled horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={styles.weather} >
        <View style={styles.day}>
          <Text style={styles.temp}>28</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>

        <View style={styles.day}>
          <Text style={styles.temp}>27</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>
        <View style={styles.day}>
          <Text style={styles.temp}>27</Text>
          <Text style={styles.desc}>Sunny</Text>
        </View>
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  conatiner: {
    flex:1,
    backgroundColor:'#b7e69c'
  },
  city: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center'
  }, 
  cityName: {
    color: 'black',
    fontSize: 38,
    fontWeight: '600'
  },  
  weather: {
  },
  day: {
    width: screenWidth,
    alignItems: 'center'
  },
  temp: {
    fontSize: 180,
    marginTop: 50,
    fontWeight: '600'
  },
  desc: {
    fontSize: 44,
    marginTop: -20
  }
})
```

로딩이 꽤 걸린다...

<br/>

**날씨**

https://openweathermap.org/api

```react
import { StatusBar } from 'expo-status-bar';
import * as Location from 'expo-location';
import React, { useEffect, useState } from 'react';
import { View, StyleSheet, Text, ScrollView, Dimensions, ActivityIndicator} from 'react-native';

const {width: screenWidth} = Dimensions.get('window');
const apiKey = '1d2a9e4353649b9974fb2b87ac1157a2'

export default function App() {
  const [region, setRegion] = useState('loading...')
  const [days, setDays] = useState([])
  const [location, setLocation] = useState()
  const [ok, setOk] = useState(true)
  const ask = async () => {
    const {granted} = await Location.requestForegroundPermissionsAsync()
    if (!granted) {
      setOk(false)
    }
    const {coords: {latitude, longitude}} = await Location.getCurrentPositionAsync({accuracy: 5})
    const location = await Location.reverseGeocodeAsync({latitude, longitude}, {useGoogleMaps: false})
    setRegion(location[0].region)
    const response = await fetch(`https://api.openweathermap.org/data/2.5/onecall?lat=${latitude}&lon=${longitude}&exclude=alerts&appid=${apiKey}&units=metrics`)
    const json = await response.json()
    console.log(json.daily)
    setDays(json.daily)
  }
  useEffect(() => {
    ask()
  }, [])
  return (
    <View style={styles.conatiner}>
      <StatusBar style="dark"/>
      <View style={styles.city}>
        <Text style={styles.cityName}>{region}</Text>
      </View>
      <ScrollView pagingEnabled horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={styles.weather} >
        {
          days.length === 0? (
          <View>
            <ActivityIndicator color='#aaa' size='large' style={styles.day}></ActivityIndicator>
          </View>
          ) : (
            days.map((day, index) => 
              <View key={index} style={styles.day}>
                <Text style={styles.temp}>{parseFloat(day.temp.day).toFixed(1)}</Text>
                <Text style={styles.desc}>{day.weather[0].main}</Text>
                <Text style={styles.tinyText}>{day.weather[0].description}</Text>
              </View>
            ))
        }
      </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  conatiner: {
    flex:1,
    backgroundColor:'#b7e69c'
  },
  city: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center'
  }, 
  cityName: {
    color: 'black',
    fontSize: 38,
    fontWeight: '600'
  },  
  weather: {
  },
  day: {
    width: screenWidth,
    alignItems: 'center'
  },
  temp: {
    fontSize: 180,
    marginTop: 50,
    fontWeight: '600'
  },
  desc: {
    fontSize: 44,
    marginTop: -20
  }, 
  tinyText: {
    fontSize: 20
  }
})
```

여기까지 하면 앱이 잘 구동된다. 나는 중간에 weather API 가져오는 것이 너무나도 오래 걸렸음. 진짜.... 계속 401 에러 떴는데 이건 시간이 해결해 주는 거랬다. 엄청 오래 기다림.

