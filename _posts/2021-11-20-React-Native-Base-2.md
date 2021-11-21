---

layout: post
Title: 독학 리액트 네이티브
tags: [study]
categories: React_Native

---

### React Native! Base 02

어제 날씨 앱이 잘 안 됐던 이유는 내가 코드를 잘못 썼음을.... 그래서 수정하였다!

아침에 일어나자마자 그것부터 수정했다. 오늘은 어제 했던 날씨 앱을 조금 이어서 한 뒤, Todo List 어플을 만들어 볼 것이다.

**아이콘 설정**

https://docs.expo.dev/guides/icons/

https://icons.expo.fyi/

아이콘은 위 두 개의 링크를 참고하였고, 실질적으로 가져온 것은 아래 링크이다. 여기서는 Filter > Fontisto 아이콘을 만들었다.

```react
import { StatusBar } from 'expo-status-bar';
import * as Location from 'expo-location';
import React, { useEffect, useState } from 'react';
import { View, StyleSheet, Text, ScrollView, Dimensions, ActivityIndicator} from 'react-native';
import { Fontisto } from '@expo/vector-icons'; 

const {width: screenWidth} = Dimensions.get('window');
const apiKey = '1d2a9e4353649b9974fb2b87ac1157a2'
const icons = {
  Clouds : "cloudy",
  Clear: "day-sunny",
  Rain: "rains",
  Snow: "snow",
  Thunderstorm: "lightning",
  Drizzle: "rain",
  Atmosphere: "cloudy-gusts"
}

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
    const response = await fetch(`https://api.openweathermap.org/data/2.5/onecall?lat=${latitude}&lon=${longitude}&exclude=alerts&appid=${apiKey}&units=metric`)
    const json = await response.json()
    // console.log(json)
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
              <View key={index} style={{...styles.day, alignItems: 'center'}}>
                <View style={{flexDirection: 'row', alignItems: 'center', width: '100%', justifyContent: 'space-around'}}>
                  <Text style={styles.temp}>{parseFloat(day.temp.day).toFixed(1)}</Text>
                  <Fontisto name={icons[day.weather[0].main]} size={68} color="black" />
                </View>

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
    fontSize: 150,
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

<br/>

<br/>

#### Todo App

이 투두 앱은 두 개의 탭이 있다. 하나는 Work 탭이고, 하나는 Groceries 탭이다.  우선 코드를 쭉 작성한 뒤 하단에 정리를 해 놓았다.

**스타일링과 헤더 영역 설정**

```javascript
// colors.js
export const theme = {
    bg: 'black',
    grey: '#3a3d40'
}
```

```react
// App.js
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TouchableWithoutFeedback, Pressable } from 'react-native';
import { theme } from './colors';

export default function App() {
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity >
          <Text style={styles.btnText}>Work</Text>
        </TouchableOpacity>
        <Pressable>
          <Text style={styles.btnText}>Groceries</Text>
        </Pressable>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    color: theme.grey,
    fontSize: 42,
    fontWeight: '800'
  }
});
```

Event 를 발생시키는 View 요소들에는 다음과 같은 것들이 있는데, 여기서는 `TouchableOpacity` 만 사용하기로 한다.

- `TouchableOpacity` 누르는 이벤트를 가지고 있는 View 이다. 

- `TouchableHighlight` 요소를 클릭했을 때 배경색이 바뀜

- `TouchableWithoutFeedback` 화면의 가장 위에서 일어나는 탭 이벤트. 그래픽이나 UI 반응은 따로 없음. 유저에게 버튼이 눌렸다는 것을 보이고 싶지 않을 때 사용 가능

- `Pressable` 더 많은 기능을 가지고 있는 것. https://reactnative.dev/docs/pressable 참고. 이것의 Props 기능 중엔 hitslope 가 자주 쓰인다. 클릭이 되는 영역을 어디까지인지 설정할 수 있다. 

<br/>

**헤더 영역 버튼 스위치 설정**

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, Pressable } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true)
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  }
});
```

이제 인풋을 만들 건데, **리액트 네이티브에는 Form 이나 input, TextArea 같은 것이 존재하지 않아서 TextInput 을 사용한다.**

그리고 아주 아주 다양한 props 가 있다. https://reactnative.dev/docs/textinput

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true)
  const [text, setText] = useState('')
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18
  }
});

```

<br/>

**버튼 누르는 것을 감지**

toDos state 는 `Object.assign()` 을 이용하여 Object 끼리 합칠 수 있게끔 작업할 것,

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const addTodo = () => {
    if (text === '') return;
    // save to do
    const newToDos = Object.assign(
      {}, 
      toDos, 
      {[Date.now()]: {text, work: working}}
    )
    setToDos (newToDos)
    setText('')    
  }
  console.log(toDos)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18
  }
});
```

스프레드 연산자를 이용하는 방법

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const addTodo = () => {
    if (text === '') return;
    // save to do
    const newToDos = {
      ...toDos, 
      [Date.now()] : {text, work: working}
    }
    setToDos (newToDos)
    setText('')    
  }
  console.log(toDos)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18
  }
});
```

결과는 같다.

<br/>

**map 으로 input 의 입력값 나타내기**

`Object.keys(x).map(key => x[key])` 를 사용. 

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput, ScrollView } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const addTodo = () => {
    if (text === '') return;
    // save to do
    const newToDos = {
      ...toDos, 
      [Date.now()] : {text, work: working}
    }
    setToDos (newToDos)
    setText('')    
  }
  console.log(toDos)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
        <ScrollView>
          {
            Object.keys(toDos).map(key => <View key={key} style={styles.toDo}><Text style={styles.toDoText}>{toDos[key].text}</Text></View>)
          }
        </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18,
    marginVertical: 20
  },
  toDo: {
    backgroundColor: theme.grey,
    marginBottom: 10,
    paddingVertical: 20,
    paddingHorizontal: 20,
    borderRadius: 15
  },
  toDoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '500'
  }
});
```

<br/>

**View 전환과 Persistence (저장소)**

```react
import { StatusBar } from 'expo-status-bar';
import React, { useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput, ScrollView } from 'react-native';
import { theme } from './colors';

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const addTodo = () => {
    if (text === '') return;
    // save to do
    const newToDos = {
      ...toDos, 
      [Date.now()] : {text, working}
    }
    setToDos (newToDos)
    setText('')    
  }
  console.log(toDos)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
        <ScrollView>
          {
            Object.keys(toDos).map(key => toDos[key].working === working ? (<View key={key} style={styles.toDo}><Text style={styles.toDoText}>{toDos[key].text}</Text></View>) : null)
          }
        </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18,
    marginVertical: 20
  },
  toDo: {
    backgroundColor: theme.grey,
    marginBottom: 10,
    paddingVertical: 20,
    paddingHorizontal: 20,
    borderRadius: 15
  },
  toDoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '500'
  }
});
```

Work 탭과 Groceries 탭을 눌렀을 때 컴포넌트가 전환이 될 수 있도록 코드를 작성해싿. 

다음은 Persistence 를 구현할 것인데, 사용자가 다른 곳에 가거나 앱을 닫았다가 돌아올 때를 위해 폰에 저장하는 것이다. 이것은 `AsyncStorage` 를 사용한다. https://react-native-async-storage.github.io/async-storage/docs/usage/

`expo install @react-native-async-storage/async-storage` 로 설치한다.

```react
import { StatusBar } from 'expo-status-bar';
import React, { useEffect, useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput, ScrollView } from 'react-native';
import { theme } from './colors';
import AsyncStorage from '@react-native-async-storage/async-storage';

const STORAGE_KEY = '@toDOs'

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  useEffect(() => {
    loadToDos()
  }, [])
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const saveToDos = async (toSave) => {
    await AsyncStorage.setItem(STORAGE_KEY, JSON.stringify(toSave))
  }
  const loadToDos = async () => {
    const s = await AsyncStorage.getItem(STORAGE_KEY)
    // string 을 Javascript Object 로 바꿔 주기 위하여 parse 사용
    setToDos(JSON.parse(s))
  }
  
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

  console.log(toDos)
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
        <ScrollView>
          {
            Object.keys(toDos).map(key => toDos[key].working === working ? (<View key={key} style={styles.toDo}><Text style={styles.toDoText}>{toDos[key].text}</Text></View>) : null)
          }
        </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18,
    marginVertical: 20
  },
  toDo: {
    backgroundColor: theme.grey,
    marginBottom: 10,
    paddingVertical: 20,
    paddingHorizontal: 20,
    borderRadius: 15
  },
  toDoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '500'
  }
});
```

브라우저의 local Storage 와 사용법은 비슷하지만 await 을 써야 한다는 점에서 차이점을 가진다.

<br/>

**항목 삭제**

리스트가 로딩 중일 때의 기능 구현과 삭제, 삭제할 때 한 번 더 확인하는 기능 구현. 

```react
import { StatusBar } from 'expo-status-bar';
import React, { useEffect, useState } from 'react';
import { StyleSheet, Text, View, TouchableOpacity, TextInput, ScrollView, Alert } from 'react-native';
import { Fontisto } from '@expo/vector-icons'; 
import { theme } from './colors';
import AsyncStorage from '@react-native-async-storage/async-storage';

const STORAGE_KEY = '@toDOs'

export default function App() {
  const [working, setWorking] = useState(true);
  const [text, setText] = useState('');
  const [toDos, setToDos] = useState({});
  useEffect(() => {
    loadToDos()
  }, [])
  const travel = () => setWorking(false)
  const work = () => setWorking(true)
  const onChangeText = payload => setText(payload)
  const saveToDos = async (toSave) => {
    await AsyncStorage.setItem(STORAGE_KEY, JSON.stringify(toSave))
  }
  const loadToDos = async () => {
    const s = await AsyncStorage.getItem(STORAGE_KEY)
    // string 을 Javascript Object 로 바꿔 주기 위하여 parse 사용
    setToDos(JSON.parse(s))
  }
  
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
  return (
    <View style={styles.container}>
      <StatusBar style="auto" />
      <View style={styles.header}>
        <TouchableOpacity onPress={work}>
          <Text style={{...styles.btnText, color: working ? 'white' : theme.grey}}>Work</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={travel}>
          <Text style={{...styles.btnText, color: !working ? 'white' : theme.grey}}>Groceries</Text>
        </TouchableOpacity>
      </View>
        <TextInput onSubmitEditing={addTodo} returnKeyType="done" value={text} onChangeText={onChangeText} autoCapitalize={'sentences'} style={styles.input} placeholder={working ? 'Add a todo' : 'What will you buy'}/>
        <ScrollView>
          {
            Object.keys(toDos).map(key => toDos[key].working === working ? (<View key={key} style={styles.toDo}><Text style={styles.toDoText}>{toDos[key].text}</Text><TouchableOpacity onPress={() => deleteToDo(key)}><Fontisto name="trash" size={18} color='#666' /></TouchableOpacity></View>) : null)
          }
        </ScrollView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.bg,
    paddingHorizontal: 20
  },
  header: {
    flexDirection: 'row',
    marginTop: 100,
    justifyContent: 'space-between'
  }, 
  btnText: {
    fontSize: 42,
    fontWeight: '800'
  },
  input: {
    backgroundColor: '#fff',
    paddingVertical: 15,
    paddingHorizontal: 20,
    borderRadius: 30,
    marginTop: 20,
    fontSize: 18,
    marginVertical: 20
  },
  toDo: {
    backgroundColor: theme.grey,
    marginBottom: 10,
    paddingVertical: 20,
    paddingHorizontal: 20,
    borderRadius: 15,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between'
  },
  toDoText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '500'
  }
});
```

<br/>

**정리**

<u>Header</u> : 두 가지 버튼 이벤트가 존재한다. Work 를 눌렀을 때, Groceries 를 눌렀을 때. 기본값은 Work 화면이 보이는 `working === true` 로 설정한다. 이 값은 Groceries 를 누르면 false 로 전환된다.

<u>Text Input</u> : input 컴포넌트. 아래와 같은 value 를 사용하여 만들었다. `ScrollView` 를 사용하여 스크롤 가능하게 설정해 주었다. `ScrollView` 에서는 state 의 구조 덕분에 `toDos.map() `을 할 수가 없었기 때문에 `Object.keys()` 를 사용했는데, 이것은 배열 자체가 아니라 key 값들만 담긴 배열을 반환한다. 그리고 그 배열에 map 을 적용한다. toDos 오브젝트에 접근하여 key 값을 추출하고 state 를 체크하여 Work 와 Groceries 의 전환이 가능하게 만들었다.

- `ReturnKeyType` : 키보드의 오른쪽 하단에 있는 리턴 버튼 디자인을 바꿈. 

- `onChangeText` : 텍스트가 바뀔 떄 실행되는 함수 text 를 받아 state 에 text 를 담음.

- `onSubmitEditing`: done 버튼을 눌렀을 때 실행되는 함수를 담음.

<u>addTodo</u> 함수의 역할 : 

1.  toDos 가 비었다면 아무것도하지 않는다. 
2. toDos 에 text 가 존재한다면 새로운 배열을 만드는데, 스프레드를 이용해 기존 배열과 새롭게 추가된 요소들을 결합하여 만들어 준다. 
3. Date.now() 를 이용하여 고윳값을 만들고 key 에 담는다. 
4. text 와 working state 를 받아 온다. 
5. addToDo 를 실행할 때 새로운 오브젝트를 생성한 뒤 state 에 저장하고 saveTodos 를 호출한다.

<u>saveToDos</u> 함수의 역할 : 

1.  `AsyncStorage` 사용. (모바일을 위한 암호화되지 않는 string을 저장하는 API. await 을 필수적으로 사용한다. try catch 를 이용하여 에러가 있는지를 반드시 확인해 주어야 한다. 그러나 여기서는 간단한 코드로 구현했기 때문에 사용하지 않음. 오브젝트였던 것을 string 으로 바꿔서 가져와야 함. 여기서 await 을 사용하는 것은 `AsyncStroage` 에 저장되는 것을 기다리기 위함이다.

<u>deleteToDo</u> 함수의 역할 : 

1. 리액트 네이티브에서 지원하는 alert API 를 사용하였다. 버튼도 사용 가능한데, 버튼의 스타일은 iOS 에서만 쓸 수 있다. 
2. 버튼을 눌렀을 때 발생시킬 callback 도 설정해 주었다. onPress 는 toDOs 값을 가지고 새로운 배열을 만들고, 이 배열의 key 값을 체크하여 그것을 지운다. 

<u>loadToDos</u> 함수의 역할 : 

1. 어플리케이션이 재시작되면 `useEffect()` 에 쓰인 loadToDos 가 실행된다.
2. `AsyncStorage` 를 이용해서 `@toDos` 를 받아 온다. 이 받아온 string 으로 저장된 정보를 parsing 하여 배열로 만들어 준다. (저장된 정보가 string 인 이유는 저장할 때 JSON.stringify 로 저장을 했기 때문.)

<br/>

<br/>
