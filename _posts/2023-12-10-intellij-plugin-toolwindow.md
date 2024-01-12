---
layout: post
title: "[IntelliJ] toolWindow Plugin 만들기"
categories: [intellij, plugin]
tags: [intellij, plugin, toolwindow]
---

## ToolWindow는?
intelliJ 좌, 우, 하단에 위치한 바에 있는 항목들을 지칭한다.

![image](https://github.com/JinseobBae/JinseobBae.github.io/assets/29051992/2098278d-47dc-4062-b4fa-9a0390555c77)

## ToolWindow 만들기
기본적인 셋팅은 되어있다는 가정하에 방법은 다음과 같다.

#### 1. 컴포넌트 만들기
---
간단하게 컴포넌트 몇가지로 구성했다.

![image](https://github.com/JinseobBae/JinseobBae.github.io/assets/29051992/ba1103fa-ea32-49a8-9c62-930914927f36)

toolWindow 생성 시 메인 panel이 필요하므로 getter를 하나 생성해준다.

```java

public class MyPluginComponent {
    private JPanel rootPanel;
    private JTextField textField1;
    private JRadioButton radioButton1;
    private JRadioButton radioButton2;
    private JCheckBox checkBox1;
    private JCheckBox checkBox2;
    private JButton button1;

    public MyPluginComponent() {
        // init
    }

    public JComponent getMainPanel() {
        return rootPanel;
    }
}

```

#### 2. ToolWindowFactory 구현하기
---
intelliJ가 제공하는 ToolWindowFactory 인터페이스 중 `createToolWindowContent`를 구현하면 된다.

```java

public class MyToolWindow implements ToolWindowFactory, DumbAware {

    @Override
    public void createToolWindowContent(@NotNull Project project, @NotNull ToolWindow toolWindow) {
        MyPluginComponent myPluginComponent = new MyPluginComponent(); // 화면 컴포넌트

        Content content = ContentFactory.getInstance().createContent( // ToolWindow에 들어갈 content 생성
            myPluginComponent.getMainPanel(), // 화면 컴포넌트의 메인 panel
            "플러그인", // toolwindow에 표기 할 명칭
            false // toolwindow에 위치 고정 여부
        );

        toolWindow.getContentManager().addContent(content); // contentManager에 추가
    }
}

```
`DumbAware` 도 함께 구현을 해줘야한다. (구현 메서드는 없어 표기만해주면된다.) indexing 할 때 dumb 모드로 해주는 설정이라고 한다.

만약 `DumbAware`를 구현하지 않으면 indexing이 완료될 때 까지 플러그인 화면이 보이지 않는다.


#### 3. ToolWindow 등록
---
plugin.xml 에 만들어진 ToolWindow를 등록한다.

```xml

<extensions defaultExtensionNs="com.intellij">
        <toolWindow factoryClass="com.example.temp.factory.MyToolWindow" id="MyTool" anchor="right"
                    icon="AllIcons.Nodes.PluginJB" canCloseContents="true"/>
</extensions>

```

#### 4. 결과 확인

![image](https://github.com/JinseobBae/JinseobBae.github.io/assets/29051992/96c79929-bbcf-426d-91b4-f3079a2433e8)



참고

- (https://plugins.jetbrains.com/docs/intellij/tool-windows.html)https://plugins.jetbrains.com/docs/intellij/tool-windows.html
