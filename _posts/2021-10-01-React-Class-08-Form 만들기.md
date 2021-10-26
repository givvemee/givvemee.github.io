---
layout: post
Title: 1001 React.js Class
tags: [study]
categories: React
---

### 오늘의 수업 : useState , props 를 이용한 Form 작성

시월의 첫 수업이었다니. 그리고 벌써 여덟번째라니...... 이제 1/3 왔는데 나의 이해도는 어디 쯤에 있을까? 이번 수업은 지난 시간에 했던 것들에 이어서 파일을 생성했다. 

<br />

먼저 오늘 만들 것은 간단한 폼 작성이었다. 총 8개의 인풋으로 구성된 폼을 작성하고 그것을 props 로 마지막 페이지에 가지고 오는 것!

![image](https://user-images.githubusercontent.com/89691274/135741776-84a6a910-3f64-4a78-8128-3fc1bb597290.png)

![image](https://user-images.githubusercontent.com/89691274/135741789-e8fbca3e-0443-4527-8978-e81ae0a4778f.png)


![image](https://user-images.githubusercontent.com/89691274/135741794-2bede793-ff2d-4580-8461-c2a593d3e023.png)

이렇게 해서 `<Survey />` 라는 커다란 틀과, 그 안에 들어갈 `<Survey 1/>` `<Survey 2/>` `<Survey 3/>` `<Survey 4/>` 를 만들었다. 순서대로 1 은 이름부터 4개의 인풋이 있는 페이지, 2는 직업부터 4개의 인풋이 있는 페이지, 3은 8개의 인풋의 value 값을 모두 받아오는 페이지, 4는 이름에서 작성된 데이터 값을 받아오는 인삿말 페이지였다.

```react
// Survey 
import React from 'react';
import './Survey.css'
import './Reset.css'
import SurveyStep1 from './SurveyStep1'
import SurveyStep2 from './SurveyStep2'
import SurveyStep3 from './SurveyStep3'
import SurveyStep4 from './SurveyStep4'
import { useState } from 'react/cjs/react.development';

const Survey = () => {
    // paging 
    const [step, setStep] = useState(1)

    const onNext = () => {
        setStep (step + 1)
    }
    const onPrev = () => {
        setStep (step - 1)
    }
    const [form, setForm] = useState({
        username: '',
        age: '',
        addr: '',
        number: '',
        job: '',
        email: '',
        gender: '',
        interest: ''

    })

    // 비구조할당
    const {username, age, addr, number, job, email, gender, interest} = form

    const changeInput = (e) => {
        const {value, name} = e.target
        // 이름 나이 주소에 해당하는 것들을 이벤트로 인식하겠다
        setForm({
            ...form,
            [name] : value
        })
    }


    return (
        <div className = "wrap">
            {
                step === 1 && 
                <SurveyStep1 form = {form} changeInput ={changeInput} onNext={onNext}/>
            }
            {
                step === 2 &&
                <SurveyStep2 form = {form }changeInput = {changeInput} onNext={onNext} onPrev={onPrev}/>
            }
            { 
                step === 3 && 
                <SurveyStep3 username={username} age={age} addr ={addr} number={number} job={job} email={email} gender={gender} interest={interest} onNext={onNext} onPrev={onPrev}/>

            }
            {
                step === 4 &&
                <SurveyStep4 username={username}/>
            }

        </div>
    );
};

export default Survey;
```

```react
// Survey 1
import React from 'react';

const SurveyStep1 = ({form, changeInput, onNext}) => {
    const {username, age, addr, number } = form
    return (
      <>
        <p>
            <label> Name : </label>
            <input type="text" value={username} name="username" onChange={changeInput}/>
            {/* onChange 주려면 name 있어야 함. 상태값 지정해서 그 이름을 key 로 씀. key 값과 동일하게 쓸 것. */}
        </p>
        <p>
            <label> Age : </label>
            <input type="text" value={age} name="age" onChange={changeInput}/>
        </p>
        <p>
            <label> Address : </label>
            <input type="text" value={addr} name="addr" onChange={changeInput}/>
        </p>
        <p>
            <label> Number : </label>
            <input type="text" value={number} name="number" onChange={changeInput}/>
        </p>
        <p>
            <button onClick={onNext}> Next</button>
        </p>
      </>
    );
};

export default SurveyStep1;
```

```react
// Survey 2
import React from 'react';

const SurveyStep2 = ({form, onNext, onPrev, changeInput}) => {
    const {job, email, gender, interest} = form 
    return (
        <>
            <p>
                <label> Job : </label>
                <input type="text" value={job} name="job" onChange= {changeInput}/>
            </p>
            <p>
                <label> Email : </label>
                <input type="text" value={email} name="email" onChange={changeInput}/>
            </p>
            <p>
                <label> Gender : </label>
                <input type="text" value={gender} name="gender" onChange={changeInput}/>
            </p>
            <p>
                <label> Interest : </label>
                <input type="text" value={interest} name="interest" onChange={changeInput}/>
            </p>
            <p>
                <button onClick={onPrev}>Prev</button>
                <button onClick={onNext}>Next</button>
            </p>
        </>
    );
};

export default SurveyStep2;
```

```react
// Survey 3
import React from 'react';

const SurveyStep3 = ({username, age, addr, number, job, email, gender, interest, onPrev, onNext}) => {
    // 여기서는 ...form 안 함 키 값을 그대로 가져오기 때무네
    return (
        <div>
            <ul>
                <li><span> Name</span> : <em>{username}</em></li>
                <li><span> Age</span> : <em>{age}</em></li>
                <li><span> Address</span> : <em>{addr}</em></li>
                <li><span> Number</span> : <em>{number}</em></li>
                <li><span> Job</span> : <em>{job}</em></li>
                <li><span> Email</span> : <em>{email}</em></li>
                <li><span> Gender</span> : <em>{gender}</em></li>
                <li><span> Interest</span> : <em>{interest}</em></li>
            </ul>
            <p>
                <button onClick={onPrev}>Prev</button>
                <button onClick={onNext}>Next</button>
            </p>
        </div>
    );
};

export default SurveyStep3;
```

```react
// Survey 4
import React from 'react';

const SurveyStep4 = ({username}) => {
    return (
        <div>
            <h3>
                <span>Hey {username},</span>Thank you for your cooperation!
            </h3>
        </div>
    );
};

export default SurveyStep4;
```

이렇게 작성하면 Survey 1 과 2 에서 작성했던 것들이 3 에 나오는 것을 확인할 수 있다. 

![image](https://user-images.githubusercontent.com/89691274/135741952-ffa84ee0-906a-4190-8aa1-6be41a2b76fc.png)

이제 직접 만들어 볼 시간...! 

<br />

<br />
