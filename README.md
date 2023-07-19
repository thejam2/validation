# validation

## 1. Formik
### 설치
` npm install formik --save`
### Formik태그
양식을 만드는 것을 도와주는 구성요소

### Formik의 props
- initialValues (form의 초기화 값. 반드시 지정해주어야 한다.)
- onSubmit (form submission handler.)
- validationSchema (yup 스키마를 리턴. 유효성 검증에 사용된다.)
- validate (yup, validationSchema 사용 안할때)

### values
form에서 공유되는 상태 값. 

### setSubmitting
onSubmit 핸들러에 setSubmitting(false)을 넣어 호출하면 주기가 완료됩니다.
onSubmit이 비동기일 경우, 자동으로 false로 지정해주어서 setSubmit(false)를 해주지 않아도 되지만, 동기일 경우에는 반드시 setSubmit(false)를 해주어야 한다.

### yup
form validation을 위한 라이브러리.

### useFormik()
모든 Formik 상태 및 도우미를 직접 반환하는 사용자 지정 React hook

### 일반 샘플코드
```
return(
  <Formik
    initialValues={{ email: "", password: "" }}
    validate={(values) => {
      const errors = {};
      if (!values.email) {
        errors.email = "Required";
      } else if (
        !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
      ) {
        errors.email = "Invalid email address";
      }
      return errors;
    }}
    onSubmit={(values, { setSubmitting }) => {
      setTimeout(() => {
        alert(JSON.stringify(values, null, 2));
        setSubmitting(false);
      }, 400);
    }}
  >
    {({ isSubmitting }) => (
      <Form>
        <label htmlFor="email">Email</label>
        <Field type="email" name="email" />
        <div className="input-feedback">
          <ErrorMessage name="email" component="div" />
        </div>
        <label htmlFor="password">Password</label>
        <Field type="password" name="password" />
        <div className="input-feedback">
          <ErrorMessage name="password" component="div" />
        </div>
        <button type="submit" disabled={isSubmitting}>
          Submit
        </button>
      </Form>
    )}
  </Formik>
);
```

### useFormik() 샘플코드
```
//useFormik방식
const formik = useFormik({
  initialValues: {
    email: "",
    password: ""
  },
  validationSchema: Yup.object({
    email: Yup.string().email("Invalid email address").required("Required"),
    password: Yup.string()
      .min(5, "Must be 5 characters or more")
      .required("Required")
  }),
  onSubmit: (values) => {
    alert(JSON.stringify(values, null, 2));
  }
});

return(
  <form onSubmit={formik.handleSubmit}>
    <label htmlFor="email">Email</label>
    <input id="email" type="email" {...formik.getFieldProps("email")} />
    {formik.touched.email && formik.errors.email ? (
      <div className="input-feedback">{formik.errors.email}</div>
    ) : null}

    <label htmlFor="password">Password</label>
    <input
      id="password"
      type="password"
      {...formik.getFieldProps("password")}
    />
    {formik.touched.password && formik.errors.password ? (
      <div className="input-feedback">{formik.errors.password}</div>
    ) : null}

    <button type="submit">Submit</button>
  </form>
);
```

## 2. React-hook-form

### 설치
`npm install react-hook-form`

### 기본샘플
```
export default function App() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm()

  const onSubmit = (data) => console.log(data)

  console.log(watch("example")) // watch input value by passing the name of it

  return (
    /* "handleSubmit" will validate your inputs before invoking "onSubmit" */
    <form onSubmit={handleSubmit(onSubmit)}>
      {/* register your input into the hook by invoking the "register" function */}
      <input defaultValue="test" {...register("example")} />

      {/* include validation with required or other standard HTML validation rules */}
      <input {...register("exampleRequired", { required: true })} />
      {/* errors will return when field validation fails  */}
      {errors.exampleRequired && <span>This field is required</span>}

      <input type="submit" />
    </form>
  )
}
```
https://www.youtube.com/watch?v=RkXv4AXXC_4

### Register fields
React Hook Form의 핵심 개념 중 하나는 register구성 요소를 후크에 연결하는 것입니다. 이렇게 하면 양식 유효성 검사 및 제출 모두에 해당 값을 사용할 수 있습니다.

참고: 각 필드는 등록 프로세스를 위한 키로 있어야 합니다. (name)

### 지원되는 유효성 검사 규칙 목록
- required
- min
- max
- minLength
- maxLength
- pattern
- validate

### Schema Validation
또한 Yup , Zod , Superstruct & Joi 를 사용하여 스키마 기반 양식 유효성 검사를 지원합니다.

여기에서 useForm 을 선택적 구성으로 schema전달할 수 있습니다.

스키마에 대해 입력 데이터의 유효성을 검사하고 오류 또는 유효한 결과를 반환합니다.

1. yup 설치
`npm install @hookform/resolvers yup`
2. 유효성 검사를 위해 스키마를 준비하고 React Hook Form으로 입력을 등록
```
const schema = yup
  .object({
    firstName: yup.string().required(),
    age: yup.number().positive().integer().required(),
  })
  .required()

export default function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(schema),
  })
  const onSubmit = (data) => console.log(data)

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName")} />
      <p>{errors.firstName?.message}</p>

      <input {...register("age")} />
      <p>{errors.age?.message}</p>

      <input type="submit" />
    </form>
  )
}
```
