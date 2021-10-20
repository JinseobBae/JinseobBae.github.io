---
layout: post
title: "[SPRING] Spring boot / Freemarker 사용 시 jspTaglib 문제 "
categories: [SPRING]
tags: [spring, freemarker]
comments: true
---

Freemarker를 사용하는 애플리케이션을 Spring boot로 이관작업을 하던 중 jspTaglib을 못읽는 현상이 발생했다.

```
<#assign st=JspTaglibs['http://www.springframework.org/security/tags']/>
```

여기서 st를 셋팅해주지 못하는게 문제였다.

Spring boot는 jsp에 대해서 공식적으로 지원하지 않는다고 한다.

그래서 구동 시 freemarker 설정을 로드 할 때 tld 파일에 대한 정보를 로드하지 않는다.

해당 tld에 대한 정보를 config에서 입력해주면 정상적으로 실행이 된다.

```java
@Configuration
public class FreemarkerExtraConfig{
 
  @Autowired
  void setTaglibFactory(FreeMarkerConfig freeMarkerConfig){
    ArrayList<String> tlds = new ArrayList<>();
    tlds.add("META-INF/security.tld");
    tlds.add("추가 할 tld");
    freeMarkerConfig.getTaglibFactory().setClasspathTlds(tlds);    
  }   
}  
```

Spring에서 freemarker config를 생성하고 난 뒤 해당 config를 인자로 받아서 tld 경로를 추가해주면 된다.

만약 freemarker config를 직접 생성해서 사용하다고 하면 시점을 잘 따져서 넣어줘야 한다.

관련 설정이 많아서 직접 config를 만들어서 사용하고 있는데, 최초 설정 시 넣어주면 오류가 난다.

```java
FreeMarkerConfigurer config = new FreeMarkerConfigurer();

config.setDefaultEncoding("UTF-8");
config.setTemplateLoaderPaths("~~~");
config.setFreemarkerSettings("~~~");
....
  
config.getTaglibFactory().setClasspathTlds(); // getTaglibFactory에서 오류난다. 
```

이렇게 설정 생성 할 때 넣어줄 수 있는 것 처럼 되어있지만

getTaglibFacotry에서 오류가 난다.

```
"No TaglibFactory available"
```

소스를 들어가서 보면  getTaglibFacotry이 null이라서 생기는 오류인데, set 시점은 다음과 같다.

```java
/**
	 * Initialize the {@link TaglibFactory} for the given ServletContext.
	 */
	@Override
	public void setServletContext(ServletContext servletContext) {
		this.taglibFactory = new TaglibFactory(servletContext);
	}
```

servletContext를 set 할 떄 taglibFactory를 생성해준다.

아마 config를 만들고 난 뒤에 해당 메서드를 실행하는 듯 하다.

config 생성 시 강제로 set을 하니 가능은했지만 나중에 또 set 하길래 그냥 후순위 작업으로 미뤄서 설정해줬다.
