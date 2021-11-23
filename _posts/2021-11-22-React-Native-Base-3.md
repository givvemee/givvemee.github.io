---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: React_Native

---

### React Native! Base 03

#### 리액트 네이티브 프로젝트를 배포하는 방법

로컬에서 **Publish or republish project...** 클릭

![image](https://user-images.githubusercontent.com/89691274/142947865-e14c997a-87d3-4e3d-ad8a-3361e33b5374.png)

이 버튼을 누르면 expo 웹 사이트로 코드가 보내진다. 입력 정보를 채우고 Publish 를 하면 링크와 함께 업로드가 완료된다. 그럼 QR 코드가 나오고, 이것을 Expo 에서 열 수 있다. (테스트 용도)

![image](https://user-images.githubusercontent.com/89691274/142948231-8c22e4eb-79f0-40d9-b12d-8ea0d01f6871.png)

<br/>

#### React Native web

`yarn add react-native-app`

React Native 코드를 이용한 프로젝트. 이것을 사용하면 앱을 위해 작성한 코드, View 뜽을 그에 상응하는 div 등으로 변경해 준다! 

https://necolas.github.io/react-native-web/

Run in web browser 를 누르면 새로운 포트가 열린다. 새로운 포트에서 지금처럼 작성된 코드를 열면 다음과 같은 오류가 뜨는데, 이것은 AsyncStorage 가 null 이기 때문에 발생한다.

![image](https://user-images.githubusercontent.com/89691274/142948668-034ec1ac-0979-4674-85dc-01e2d58e025d.png)

그래서 코드를 조금 수정해 주어야 함.

```react
(...)
  
  const addTodo = async () => {
    if (text === '') return;
    // save to do
    const newToDos = {
      ...toDos, 
      [Date.now()] : {text, working}
    }
    setToDos(newToDos)
    await saveToDos(newToDos)
    setText('')    
  }
  const deleteToDo = async (key) => {
    Alert.alert('Delete To do', 'Are you sure?', 
    [{
      text: 'Hmm...'
    },{
      text: "Sure!", 
      style: 'destructive',
      onPress: async () => {
        const newToDos = {...toDos}
        delete newToDos[key]
        setToDos(newToDos)
        await saveToDos(newToDos)
      }
    }])
   
  }

  console.log(toDos)
(...)
```

이렇게 코드를 수정하면 

![image](https://user-images.githubusercontent.com/89691274/142948821-cb9d2e45-b6a4-4416-b5a2-d50da523345a.png)

웹에 이렇게 나온다. styleSheet 는 작동이 잘 안 되기 때문에 style 요소로 대체해 주거나, style 은 그때그때 찾아서 수정만 하면 됨!

<br/>

**버그 수정**

앱으로 만든 프로젝트를 웹에서 열게 되면 약간의 버그가 생길 수 있다. 현재 프로젝트에서는 대표적으로 Alert 가 되지 않는 현상. 웹페이지로 뜬 프로젝트에서 쓰레기통 버튼을 누르면 삭제 문구가 생기지 않으므로 수정이 조금 필요하다.

여기서는 Platform 이라는 react native API 를 사용할 것이다. 

https://reactnative.dev/docs/platform

import 로 Platform 를 import 하고 DeleteToDo 를 수정해 준다. 우리가 웹에 있는지를 확인해서 웹에 있을 땐 alert 를 표현.

```react
import { StatusBar } from 'expo-status-bar';
import React, { useEffect, useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput, ScrollView, Alert, Platform } from 'react-native';
import { Fontisto } from '@expo/vector-icons'; 
import { theme } from './colors';
import AsyncStorage from '@react-native-async-storage/async-storage';
const STORAGE_KEY = '@toDOs'

(...)
  const deleteToDo = async (key) => {
    if(Platform.OS === 'web') {
      const ok = confirm('Are you sure to delete this to do?') 
      if (ok) {
        const newToDos = {...toDos}
        delete newToDos[key]
        setToDos(newToDos)
        await saveToDos(newToDos)
      }
    } else {
      Alert.alert('Delete To do', 'Are you sure?', 
      [{
        text: 'Hmm...'
      },{
        text: "Sure!", 
        style: 'destructive',
        onPress: async () => {
          const newToDos = {...toDos}
          delete newToDos[key]
          setToDos(newToDos)
          await saveToDos(newToDos)
        }
      }])
    }
   
  }

(...)
```

이렇게 하면 Website 에서도 쓰레기통을 누르면 알림 창이 뜬다. 그리고 다시 Publish.

<br/>

**업데이트를 업데이트 하기**

다시 Publish 를 해 주면 된다. 

<br/>

**App.json 업데이트 하기**

어플리케이션을 내가 원하는 대로 만들기 위해 편집해야 하는 파일.

https://docs.expo.dev/workflow/configuration/ 이곳에서 넣을 수 있는 정보를 확인할 수 있다. 

여기서 아이콘과 스플래쉬를 바꿀 수 있다. 루트 디렉터리의 assets 디렉터리에 현재 사용되고 있는 스플래쉬와 아이콘 정보를 확인할 수 있다. 

<br/>

**App Store 에 배포하기**

https://docs.expo.dev/classic/building-standalone-apps/

앱스토어는 기본적으로 $99 달러를 내야 한댄다.... 하지만 기본적으로는 Expo Cli 를 이용하여 해결할 수 있다. 

터미널에서 `expo build:ios` 를 먼저 해 보자.

그러면 여러가지 질문이 나오고, 그에 대한 대답을 해 주면 된다. (지금은 내가 Apple Developer 에 등록되지 않아서 할 수가 없는 듯!) 그러면 build 가 되는데, 이 모든 것을 Expo 가 해 주는 것이다.

<br/>

**웹사이트에 배포하기**

https://docs.expo.dev/distribution/publishing-websites/

나의 경우에는 github 에 배포할 것이다. 

1. Github 에 새로운 레포 생성

2. `yarn add -D gh-pages`

3. `git init`

4. `git remote add origin http://{username}/githun.io/{projectname}`

5. `package.json` 파일을 열어 homepage 주소를 추가

6. ```json
   "scripts": {
           "deploy": "gh-pages -d web-build",
           "predeploy": "expo build:web"
       }
   ```

   추가

7. `yarn deploy` 까지 하면 

8. ![image](https://user-images.githubusercontent.com/89691274/142952175-6687d94f-221b-42a9-967f-2ba989d20dc7.png)

완성! 

내 건 <a href="https://givvemee.github.io/react_native_practive_todo/">여기</a>에 배포했당. 

<br/>

<br/>

#### Expo 의 문제

Expo 는 앱 설정에 관하여 많은 것을 설정할 수 없다. 디렉터리를 살펴보면 나는 Javascript 만 쓸 수 있고, app.json 안에 많은 것을 설정할 수 있지만, **큰 문제는 기본적인 파일들에 접근을 할 수 없다는 것.** 

SDK 에서 찾을 수 없는 라이브러리들을 적용할 때는 Expo 가 아닌 https://reactnative.dev/blog/2017/03/13/introducing-create-react-native-app 이런 것이 선택지가 될 수도 있다. 
