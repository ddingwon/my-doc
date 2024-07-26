# Git 설정

Docusaurus 프로젝트를 로컬 PC에서 Git 저장소에 푸시하는 방법은 다음과 같습니다. 아래 단계들을 따라주세요:

1. **Git 설치**:
   - Git이 설치되어 있지 않다면 [Git 공식 사이트](https://git-scm.com/)에서 설치하세요.

2. **Git 저장소 초기화**:
   - 터미널을 열고 Docusaurus 프로젝트 디렉토리로 이동합니다.
   ```bash
   cd /home/ubuntu/my-docs/.nvm/my-website
   ```
   - Git 저장소를 초기화합니다.
   ```bash
   git init
   ```

3. **원격 저장소 추가**:      /* 생성되어 있으면 생략*/
   - GitHub나 GitLab 등에서 새로운 저장소를 생성합니다.
   - 생성된 저장소의 URL을 사용하여 원격 저장소를 추가합니다.
   ```bash
   git remote add origin https://github.com/ddingwon/my-doc.git
   ```

4. **파일 추가 및 커밋**:
   - 모든 파일을 추가합니다.
   ```bash
   git add .
   ```
   - 커밋 메시지를 작성하여 커밋합니다.
   ```bash
   git commit -m "Initial commit"
   ```

5. **원격 저장소에 푸시**:
   - 메인 브랜치(main 또는 master)에 푸시합니다.
   ```bash
   git push -u origin main
   ```

:::info
보안상의 이유로 'git push' 명령 수행시 에러가 발생할 수 있음. GitHub는 2021년 8월 13일부터 비밀번호 인증을 중단하고, 대신 개인 액세스 토큰(Personal Access Token)이나 SSH 키를 사용한 인증을 권장합니다. 다음은 개인 액세스 토큰을 사용하여 인증하는 방법입니다.
:::


### 개인 액세스 토큰 생성 및 사용

1. **GitHub에서 개인 액세스 토큰 생성**:
   - [GitHub 설정 페이지](https://github.com/settings/tokens)로 이동합니다.
   - "Generate new token" 버튼을 클릭합니다.
   - 토큰의 이름, 만료 날짜를 지정하고 필요한 권한(예: `repo`)을 선택합니다.
   - "Generate token"을 클릭하여 토큰을 생성합니다.
   - 생성된 토큰을 복사합니다. 이 토큰은 다시 확인할 수 없으므로 안전한 곳에 저장하세요.

2. **Git에서 개인 액세스 토큰 사용**:
   - 기존의 원격 URL을 변경하여 토큰을 포함합니다.
   ```bash
   git remote set-url origin https://<your-token>@github.com/ddingwon/my-doc.git
   ```
   예를 들어, 토큰이 `ghp_ABC1234XYZ`라면:
   ```bash
   git remote set-url origin https://ghp_ABC1234XYZ@github.com/ddingwon/my-doc.git
   ```

3. **푸시 명령어 실행**:
   - 이제 변경된 URL을 사용하여 푸시를 시도합니다.
   ```bash
   git push -u origin main
   ```

### SSH 키를 사용한 인증

SSH 키를 사용하는 것도 좋은 방법입니다. SSH 키를 설정하는 방법은 다음과 같습니다:

1. **SSH 키 생성**:
   - 터미널에서 다음 명령어를 사용하여 SSH 키를 생성합니다.
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   프롬프트가 나오면 기본 경로를 사용하고, 필요에 따라 패스프레이즈를 설정합니다.

2. **SSH 키 추가**:
   - 생성된 SSH 키를 클립보드에 복사합니다.
   ```bash
   cat ~/.ssh/id_ed25519.pub | pbcopy   # macOS
   cat ~/.ssh/id_ed25519.pub | xclip    # Linux
   type ~/.ssh/id_ed25519.pub | clip    # Windows
   ```
   - GitHub의 [SSH 및 GPG 키 설정 페이지](https://github.com/settings/keys)로 이동하여, "New SSH key"를 클릭한 다음 클립보드의 키를 추가합니다.

3. **원격 URL을 SSH로 변경**:
   ```bash
   git remote set-url origin git@github.com:ddingwon/my-doc.git
   ```

4. **푸시 명령어 실행**:
   ```bash
   git push -u origin main
   ```

이 단계를 통해 인증 문제를 해결할 수 있을 것입니다. 도움이 필요하면 언제든지 질문하세요!


## Git Usage

1. 현재 브랜치 확인:
   ```bash
   git branch
   ```
   만약 브랜치 이름이 `main`이 아닌 다른 이름(`master` 등)이면 해당 브랜치를 사용해야 합니다.

2. 현재 브랜치가 `master`라면, `main` 브랜치를 생성하고 푸시할 수도 있습니다:
   ```bash
   git branch -M main
   ```

3. 다시 원격 저장소에 푸시 시도:
   ```bash
   git push -u origin master
   ```

이렇게 해도 문제가 해결되지 않으면, 다음 추가 단계들을 시도해 볼 수 있습니다:

1. 모든 변경사항이 커밋되었는지 확인:
   ```bash
   git status
   ```
   만약 커밋되지 않은 변경사항이 있다면 커밋합니다:
   ```bash
   git add .
   git commit -m "Save current progress"
   ```

2. 기본 브랜치 이름을 `main`으로 변경하고 푸시:
   ```bash
   git branch -M main
   git push -u origin main
   ```

## 다른 PC 혹은 장소에서 작업

두 대의 PC에서 동일한 Git 저장소를 사용하는 방법은 다음과 같습니다. 이 과정에서는 원격 저장소를 클론하고, 변경 사항을 푸시 및 풀하는 방법을 설명합니다.

### 1. 두 번째 PC에서 Git 저장소 클론하기
먼저, 두 번째 PC에서 GitHub 저장소를 클론합니다.

1. 터미널(또는 Git Bash)을 엽니다.
2. 저장소를 클론할 디렉토리로 이동합니다.
   ```bash
   cd /path/to/your/desired-directory
   ```
3. GitHub 저장소를 클론합니다.
   ```bash
   git clone https://github.com/ddingwon/my-doc.git
   ```
   만약 SSH를 사용하는 경우:
   ```bash
   git clone git@github.com:ddingwon/my-doc.git
   ```

### 2. 두 번째 PC에서 작업 및 푸시
클론한 후, 작업을 수행하고 변경사항을 푸시하는 방법은 다음과 같습니다.

1. 클론한 저장소 디렉토리로 이동합니다.
   ```bash
   cd my-doc
   ```

2. 파일을 수정하거나 추가합니다.

3. 변경 사항을 스테이징합니다.
   ```bash
   git add .
   ```

4. 변경 사항을 커밋합니다.
   ```bash
   git commit -m "설명 메시지"
   ```

5. 변경 사항을 푸시합니다.
   ```bash
   git push origin main
   ```

### 3. 첫 번째 PC에서 변경 사항 가져오기
첫 번째 PC에서도 두 번째 PC에서 푸시한 변경 사항을 가져와야 합니다. 이를 위해 pull 명령어를 사용합니다.

1. 터미널(또는 Git Bash)을 엽니다.
2. 프로젝트 디렉토리로 이동합니다.
   ```bash
   cd /path/to/your/docusaurus-project
   ```
3. 원격 저장소의 변경 사항을 가져옵니다.
   ```bash
   git pull origin main
   ```

이 과정을 반복하면 두 대의 PC에서 동일한 저장소를 사용하여 작업할 수 있습니다.

### 요약
1. 두 번째 PC에서 저장소 클론:
   ```bash
   git clone https://github.com/ddingwon/my-doc.git
   ```

2. 두 번째 PC에서 작업 후 푸시:
   ```bash
   git add .
   git commit -m "설명 메시지"
   git push origin main
   ```

3. 첫 번째 PC에서 변경 사항 풀:
   ```bash
   git pull origin main
   ```

