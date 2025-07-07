# Git 브랜치 전략

<br />

## 🌵Git Flow

Git Flow에서 개발 기능은 `feature` 브랜치에서 이루어지고, 완료된 후 `develop` 브랜치에 병합됩니다. 릴리스를 준비할 때는 `release` 브랜치에서 QA와 최종 검증을 거친 뒤, 프로덕션 코드를 관리하는 `main`에 병합됩니다. 긴급한 수정 사항이 생기면 `hotfix` 브랜치를 만들어 신속히 배포하고, 수정사항을 `develop` 브랜치에도 반영합니다.

<div align="center">
  <img width="50%" src="https://github.com/user-attachments/assets/ecb836a8-e443-4843-a951-d8a72f095804" />
</div>

Git Flow를 이용하면 대규모 프로젝트에서 체계적이고 안정적인 관리가 가능하지만, 브랜치가 많아짐에 따라 복잡도가 올라간다는 단점이 존재합니다.

<br />

## 🎋 GitHub Flow

GitHub Flow는 모든 변경사항이 `main` 브랜치 기준으로 이루어지며, 새로운 기능을 개발할 때 `feature` 브랜치를 생성한 뒤 작업이 끝나면 코드 리뷰를 받고 `main` 브랜치에 병합합니다.

GitHub Flow 방식은 간소화된 프로세스를 가지고 있기 때문에 짧은 주기의 배포 환경에서 특히 유용하지만, 릴리스와 QA를 위한 별도의 브랜치가 없어 안정성 관리가 중요한 프로젝트에서는 다소 부담스러울 수 있습니다.

<br />

## 🪵 Trunk-Based Development

Trunk-Based Development는 `main` 브랜치 하나만 운용하는 방식입니다. 작업을 `main` 브랜치에 직접 커밋하거나, `feature` 브랜치를 만들고 며칠 내에 빠르게 병합합니다.

Trunk-Based Development는 병합 주기가 짧아 충돌 가능성이 적다는 장점이 있지만 철저한 자동화 환경이 뒷받침되어야 한다는 단점이 존재합니다.

<br />

## 📖 참고

- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/)
