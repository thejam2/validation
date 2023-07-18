# validation

## 1. Formik
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
