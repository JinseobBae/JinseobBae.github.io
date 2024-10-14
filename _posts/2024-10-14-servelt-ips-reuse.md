---
layout: post
title: "[Spring] request의 inputstream (body) 재활용"
categories: [java, spring]
tags: [java, spring]
---

Spring에서 `@RequestBody` 를 이용해 body 데이터를 받을 떈 내부적으로 HttpServletRequest의 inputStream에서 값을 가져온다.

inputStream은 흐름이기때문에 미리 마킹해놓지 않으면 `read()`로 한 번 읽은 내용을 다시 읽을 수 없다. 

때문에 `@RequestBody`로 선언한 객체가 만들어지기 전에 먼저 `inputStream.read()`를 실행시키면 이 후 값을 읽을 수 없어서

`Required request body missing` 이라는 에러가 나온다. 

그래서 inputStream에 있는 body의 내용을 다시 사용 할 수 있게 request를 수정했다.

## HttpServletRequestWrapper 

대부분의 글이 `HttpServletRequestWrapper`를 이용해서 처리했고, 그 방법을 따랐다.

HttpServletRequestWrapper를 상속받아서 inputStream에서 정보를 가져오는 부분만 수정을 한다.

`getInputStream()`과 `getReader()` 2개를 재구현해주면 된다.

```Java
public class MyCustomRequest extends HttpServletRequestWrapper {

  private final byte[] body; // 전달받은 body 데이터

  public MyCustomRequest(HttpServletRequest request) throws IOException {
    super(request);
    body = request.getInputStream().readAllBytes(); // body 데이터 read
  }

  @Override
  public ServletInputStream getInputStream() throws IOException {
        final ByteArrayInputStream inputStream = new ByteArrayInputStream(body); // 생성자에서 읽어놓은 body로 다시 inputStream 재생성
        return new ServletInputStream() {
            @Override
            public boolean isFinished() {
                return inputStream.available() == 0;
            }

            @Override
            public boolean isReady() {
                return true;
            }

            @Override
            public void setReadListener(ReadListener readListener) {

            }

            @Override
            public int read() throws IOException {
                return inputStream.read();
            }
        };
  }

  @Override
    public BufferedReader getReader() throws IOException {
        return new BufferedReader(new InputStreamReader(this.getInputStream(), StandardCharsets.UTF_8));
    }
} 

```

생성자에서 ServletRequest를 받아서 inputStream 바이트를 저장하고 

request의 `getReader()`를 호출 할 때 마다 그 데이터로 다시 ByteArrayInputStream를 재생성해주는 방식이다.


