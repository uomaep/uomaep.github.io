---
title: "neovim으로 spring boot gradle refresh 방법"
description: Neovim으로 Spring Boot 개발 시 의존성 추가 후 gradle refresh 하는 방법
author: uomaep
categories: [Neovim]
tags: [Vim, Neovim, LazyVim]
date: 2025-01-09 01:00:00 +0900
typora-root-url: ../
---

![screenshot-6353282](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6353282.png)

SpringBoot 프로젝트에서 `ObjectMapper` 클래스를 사용하려고 하는데 의존성이 추가되어 있지 않아서 import가 안되는 상황이다.

우선 `build.gradle`에 필요한 의존성을 추가해보자.

![screenshot-6353444](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6353444.png)

```build.gradle
	implementation 'org.springframework.boot:spring-boot-starter-json'
```

이렇게 의존성을 추가해주면 `intelliJ` 에선 아래와 같이 `load-gradle-changes` 버튼을 통해서 로드할 수 있다.

 ![screenshot-6353660](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6353660.png)

그렇다면 Neovim으로 개발 중이라면 어떻게 로드를 할 수 있을까? 우선 어떠한 방법을 하지 않고 그냥 로드가 되는지 확인해보자.

![screenshot-6353839](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6353839.png)

여전히 import가 안된다고 한다. 그냥 겉으로만 그럴 수 있으니 실행도 해보자.

![screenshot-6353929](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6353929.png)

역시 안된다. 당연한 결과이다. 왜냐면 import문도 없기 때문이다. import문을 수동으로 직접 넣어줘보자.

![screenshot-6354038](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354038.png)

import문을 수동으로 직접 넣어줬다. 실행도 해보자.

![screenshot-6354104](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354104.png)

실행은 잘된다. 결국 실행할 땐 의존성 적용이 잘 된다는 것이다. 하지만 우리가 에디터를 쓰는 이유는 코딩을 편하게 하기 위함이다. 자동완성, 자동 import ... 등 편리한 기능이 안된다면 Neovim을 굳이 쓸 이유가 없다.

Neovim에는 intelliJ에서 제공하는 파란 gradle 코끼리 버튼이 없다. 그래서 찾아본 결과 `Eclipse` 플러그인을 이용하는 것이다. `Gradle Eclipse Update`을 통해 간접적으로 classpath를 새로 고치도록 하는 방법으로 해결할 수 있다.

우선 `build.gradle` 파일에 `plugins`에 `eclipse`를 추가한다.

``` 
plugins {
	...
	id 'eclipse'
}
```

![screenshot-6354635](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354635.png)

그 다음 프로젝트 폴더에서 Gradle Eclipse 태스크를 실행한다.

```shell
./gradlew eclipse
```

위 명령어를 통해 태스크를 실행하면 `.classpath`와 `.project` 파일이 갱신되어 반영된다.

![screenshot-6354781](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354781.png)

그러면 정상적으로 Neovim에서도 의존성이 반영되는 것을 볼 수 있다. import문과 코드를 지우고 자동 문법 완성이 뜨는지 보자.

![screenshot-6354843](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354843.png)

잘뜬다. Enter를 누르면 자동 Import도 된다. 박수 짝짝ㅉㅉㅉ



하지만, 의존성을 추가할 때마다 매번 터미널에서 명령어로 Eclipse 태스크를 실행하려면 너무 귀찮지 않을까? 단축키를 설정해보자.

![screenshot-6354994](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6354994.png)

필자의 Neovim keymap 설정 파일이다.

```lua
... 그 외 설정

-- Gradle Refresh
--
local function isSpringBootProject()
  local buildFile = "build.gradle"
  if vim.fn.filereadable(buildFile) == 0 then
    return false
  end
  local lines = vim.fn.readfile(buildFile)
  for _, line in ipairs(lines) do
    if line:find("org.springframework.boot") then
      return true
    end
  end
  return false
end

keymap.set("n", "<leader>r", function()
  if isSpringBootProject() then
    vim.cmd("!./gradlew eclipse")
  else
    vim.notify("Not Spring Boot Project!", vim.log.levels.INFO)
  end
end, { noremap = true, silent = true, desc = "Run Gradle eclipse and restart LSP if Spring Boot project" })
```

`isSpringBootProject()` 함수는 현재 열고 있는 파일이 Spring Boot Project의 파일인지 확인하는 함수이다.  Neovim만의 파란 코끼리 단축키를 만드는 것이기 때문에 Spring Boot Project에서만 실행되도록 해야겠죠?

그 다음 `keymap.set("n", "<leader>r", function())` 부분을 보면 Spring Boot Project가 맞는지 확인한 다음 맞으면 터미널에서 `./gradlew eclipse` 명령, 즉 Eclipse 태스크를 실행하도록 하고, 아니면  `Not Spring Boot Project!` 메시지를 띄우도록 했다.

`keymap.set("n", "<leader>r", function())` 에서 `"n"`은 Vim으로 열었을 때 `normal` 모드일 때, `"<leader>r"`은 필자의 경우 `<leader>`키가 `SpaceBar`키이다. 즉, `SpaceBar` + `r` 키를 누르면 내가 만든 코끼리 단축키가 실행되는 것이다. 그러면 써보자!

![screenshot-6355374](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6355374.png)

단축키를 눌렀더니 오른쪽 위에 보면 `BUILD SUCCESSFUL` 메시지가 뜬다. 성공이다. 이렇게 classpath를 새로 갱신해야 할 때면 간단하게 단축키를 통해서 할 수 있게 되었다. 코딩 속도 UP!

이번엔 Spring Project가 아닌 파일에서 실행해보자.

![screenshot-6355475](/assets/images/posts_img/2025-01-09-neovim-gradle-refresh/screenshot-6355475.png)

이 파일은 코테 문제 푸는 용인 cpp 파일인데, 단축키를 누르니 오른쪽 위에 `Not Spring Boot Project!`라고 메시지가 잘뜬다.

이로써 Neovim에서도 Spring Boot 개발을 잘 할 수 있을 것 같다. 

Neovim엔 수많은 기능 좋은 플러그인이 있는데 잘 활용하면 vscode나 intelliJ 안써도될 정도로 다 할 수 있어서 Neovim 써보는 거 추천드린다.