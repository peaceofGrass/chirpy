---
title: Github 블로그 만들기 (chirpy 테마)
author: roger park
date: 2022-06-17
categories: [github blog]
tags: [chipy, git bash, jekyll, ruby, bundler, proxy]
pin: true
---

아래는 내가 원하는 테마의 블로그를 만들기까지의 험난했던 과정에서 기억할 필요가 있을 만한 것들을 정리한것.

## 1. github 계정 생성 (자세한 설명은 생략...)
## 2. git bash 설치 (커맨드 라인에서 clone, commit, push 등 작업이 가능) (자세한 설명은 생략...)
## 3. 원하는 테마 fork 해오기.
참고로 내가 선택한 테마는 [chirpy](http://jekyllthemes.org/themes/jekyll-theme-chirpy/), 즉 현재 테마이다.
<!-- ![chirpy 이미지](https://cdn.jsdelivr.net/gh/cotes2020/chirpy-images/commons/devices-mockup.png). -->
fork 한 다음 로컬에서 git clone 을 통해 소스를 로컬에 저장함.

## 4. jekyll 을 설치하여 로컬에서 실행하기
### ruby 설치하기
jekyll 은 ruby 기반이라 설치가 필요하다.
ruby는 [https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/) 에서 다운로드가 가능하다.
<br />
<br />
아래는 jekyll 설치에 관해 참고했던 블로그이다.
[https://theorydb.github.io/envops/2019/05/03/envops-blog-github-pages-jekyll/#github-%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85-%EB%B0%8F-fork](https://theorydb.github.io/envops/2019/05/03/envops-blog-github-pages-jekyll/#github-%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85-%EB%B0%8F-fork)
<br />
<br />
위 링크속 글에서 사용한 버전은 Ruby+Devkit 2.5.5-1 (x64). 나중에 설치를 하고 jekyll 을 실행 시키려 했더니
테마의 버전을 따라가지 못해서 다시  Ruby+Devkit 2.7.6-1 (x64) 이 버전으로 설치를 했다.
<br />
<br />
참고로 그 때 떴던 에러는:
```
html-proofer (~> 3.18)  # 이 문구가 포함된 제시어였음. 제시대로 bundle install을 실행했지만 설치에 실패.
```
어찌저찌 ruby는 설치했고, 계속해서 아래 명령을 실행한다.<br />

### jekyll 설치하기

인코딩을 부여하기 명령이라는데 일단 하라는대로 해보는거다.
```
>chcp 65001
```
다음은 위에서 clone 했던 저장소의 위치로 이동한 후 <br />
ruby에 내재된 gem 명령을 통해 jekyll 의 실행에 필요한 아래의 패키지들을 설치한다.
- bundler
- jekyll
- minima
- jekyll-feed
- tzinfo-data
- rdiscount

```
gem install bundler jekyll minima jekyll-feed tzinfo-data rdiscount # 요 한줄로 가능
```

무사히 설치되었으면 좋았겠지만! 아래와 같은 에러가 떴다.
```
ERROR:  While executing gem ... (URI::InvalidURIError)
    bad URI(is not URI?):127.0.0.1:10809
```
해결하는데 애를 많이 먹었지만 문제는 생각보다 간단했는데.
그것은 바로 내가 사용하고 있는 우회 프로그램 v2rayN 때문이었다!
<br />
gem 명령에 대해 설명을 해보자면 `.gemrc` 라는 파일이 존재한다.
windows 에서는 `C:\Users\사용자명` 디렉토리에 기본적으로 ruby 설치시에
생성이 되거나 수동으로 생성을 해줘야 하는것 같다. (자세한건 나도 모름...)
<br />
파일의 내용은 아래의 포맷을 따르는데 여기서 중요한것이 `sources` 아래에 있는 url이다.
```
---
:backtrace: false
:bulk_threshold: 1000
:sources:
- https://rubygems.org/
- https://ruby.taobao.org/
- https://gems.ruby-china.org
:update_sources: true
:verbose: true
:concurrent_downloads: 8
```

gem 명령을 실행할 때 바로 `sources` 에 포함된 url을 참고하여
설치하고자 하는 패키지를 찾아서 설치를 해주는 정도로 이해를 하고 있다.
<br />
<br />
그런데 gem 명령 실행시 곧바로 `https://rubygems.org/` 로 방문하지
않고 `v2rayN` 에서 사용하는 http 프락시 `127.0.0.1:10809` 로 우회를
해서 방문하는 도중에 문제가 생긴 듯 하다. (자세한건 나도 모름...)
![v2rayN-proxy]({{site.url}}/assets/img/blog/v2ray_http.PNG)
<br />
그래서 찾은 해결 방법은 아래의 명령을 통해 http 프락시를 제거해버렸다.
```
set http_proxy=     // 프락시 취소/설정. =뒤의 값이 빈 값이 취소다.
set http_proxy      // 프락시 보기
```
다시 `gem install bundler jekyll minima jekyll-feed tzinfo-data rdiscount`
요 명령을 실행했더니 문제없이 설치 되었다.
<br>
<br>
마지막으로 로컬에서 `jekyll` 을 실행시킨다.
```
bundle exec jekyll serve
```

실행하면 아래와 같은 화면이 뜬다.
```
Configuration file: D:/project/blog/jekyll-theme-chirpy/peaceofGrass.github.io/_config.yml
 Theme Config file: D:/project/blog/jekyll-theme-chirpy/peaceofGrass.github.io/_config.yml
            Source: D:/project/blog/jekyll-theme-chirpy/peaceofGrass.github.io
       Destination: D:/project/blog/jekyll-theme-chirpy/peaceofGrass.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 2.015 seconds.
 Auto-regeneration: enabled for 'D:/project/blog/jekyll-theme-chirpy/peaceofGrass.github.io'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```
`Server address` 의 url을 브라우저에 입력하면 실행되고 있는 블로그를 볼수 있다.

## 5. github page 로 실행하기
로컬에서 잘 돌아가니 github 에서도 잘 돌아갈줄 알았건만
아래와 같은 문제들이 차례로 발생했다.
![index 페이지 에러](https://user-images.githubusercontent.com/41880719/169641692-32a1f04e-06ce-4acf-b492-6b95cd015979.png)
![404 에러](https://aws1.discourse-cdn.com/github/optimized/2X/8/83de8937fa5bfacb4122979ad6903c94361eacc5_2_1035x580.png)
<br>
<br>
이 역시 나중엔 해결이 되었지만 정확히 원리가 무엇인지는 더 알아볼 필요가 있다.
아래의 방법들로 문제는 해결되었다. github page 가 push 된 후 몇 분 동안의
딜레이가 존재하므로 정확히 어떤 방법에 의해 해결되었는지 정확히 알수 없다.
이 땐 딜레이가 그렇게 긴지 몰랐음.

### 1. 프로젝트의 루트 디렉토리에서 .travis.yml 파일 삭제후 commit & push.
### 2. 프로젝트의 루트 디렉토리에서 `.github\workflows\pages-deploy.yml.hook` 파일의 이름중
`.hood` suffix 를 제거후 commit & push.
### 3. git bash 에서 아래 명령을 실행.
```
$ git commit --allow-empty -m "Trigger rebuild"
$ git push
```
### 4. 로컬에서 임의의 코드를 수정후 commit & push 했을 때 github 측에서
`gh-pages` 브랜치가 새로 생성되고 이 브랜치로 배포해야 정상적으로 작동한다.
![gh-pages 브랜치]({{site.url}}/assets/img/blog/gh-pages-branch.PNG)

> 이 밖에도 자잘한 문제들이 많았지만 주요하게 기억에 남는 것들은 위에서 모두 설명했다.
{: .prompt-tip }

# -끝-
