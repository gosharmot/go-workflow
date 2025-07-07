### Add secrets
```zsh
gh secret set GIT_PUSH_TOKEN -b "your_token_here" -R org/repo && \
gh secret set CODECOV_TOKEN -b "your_token_here" -R org/repo
```

### Test

#### Call example
```yaml
jobs:
  build-and-push:
    uses: gosharmot/go-workflow/.github/workflows/test.yaml@main
    with:
      go-version: '1.22'
    secrets:
      GIT_PUSH_TOKEN: ${{ secrets.GIT_PUSH_TOKEN }}
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```

#### Codeconv
```yaml
coverage:
  status:
    project:
      default:
        target: auto
        threshold: 1%
        informational: true
    patch:
      default:
        target: auto
        threshold: 0%
        informational: true

comment:
  layout: "reach,diff,flags,files,footer"
  behavior: default
  require_changes: true
  branches:
    - "!main"
    - "*"
  hide_project_coverage: false
  show_carryforward_flags: true

parsers:
  gcov:
    branch_detection:
      conditional: yes
      loop: yes
      method: yes
      macro: no

ignore:
  - "**/*.pb.go"
  - "**/mocks/**"
```

### Docker build

#### Call example
```yaml
jobs:
  docker-build:
    needs:
      - set-tag
    uses: ./.github/workflows/docker.yaml
    with:
      binary-name: ${{ name }}
      image-name: ${{ github.repository }}:${{ tag }}-${{ github.sha }}
      registry-host: ${{inputs.registry-host}}
      main-file: ./cmd/${{ name }}/*.go
    secrets:
      GIT_PUSH_TOKEN: ${{secrets.GIT_PUSH_TOKEN}}
```

#### Dockerfile
```dockerfile
FROM alpine

COPY --from=builder /app .

CMD /app
```

### GO

#### Call example
```yaml
jobs:
  go:
    needs: set-tag
    uses: gosharmot/go-workflow/.github/workflows/go.yaml@go-workflow
    with:
      go-version: '1.24'
      registry-host: 'ghcr.io'
    secrets:
      GIT_PUSH_TOKEN: ${{ secrets.GIT_PUSH_TOKEN }}
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```