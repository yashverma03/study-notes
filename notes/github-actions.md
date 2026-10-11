# Intro

CI/CD platform to automate build, test and deployment pipelines

Added in .github/workflows directory

GHCR (GitHub Container Registry)- GitHub’s built-in service to store, version, and distribute container images

Change failure rate- Percentage of production deployments that cause a failure

Mean Time To Recovery (MTTR)- Average time taken to restore service after a failure


## Common actions

| Description | Action |
| --- | --- |
| Checks out your repository code into the runner | `actions/checkout@v4` |
| Sets up Node.js of specific version on runner | `actions/setup-node@v4` |
| Caches dependencies between runs | `actions/cache@v4` |
| Uploads files for later use | `actions/upload-artifact@v4` |
| Downloads previously stored artifacts | `actions/download-artifact@v4` |
| Authenticates to a Docker registry | `docker/login-action@v3` |
| Builds & pushes Docker images | `docker/build-push-action@v4` |
| Sets up kubectl CLI | `actions/setup-kubectl@v4` |
| Sets up Helm CLI | `azure/setup-helm@v4` |
| Configures AWS creds | `aws-actions/configure-aws-credentials@v4` |
| Filters changes by path | `dorny/paths-filter@v3` |


# Example

```
name: Build Docker Image and Deploy to EKS

# ------------------------------------------------------------
# TRIGGERS
# ------------------------------------------------------------
on:
  push:
    branches: [ "main" ]
  workflow_dispatch:

# ------------------------------------------------------------
# REQUIRED PERMISSIONS
# ------------------------------------------------------------
permissions:
  contents: write       # create git tags
  packages: write       # push to GHCR

# ------------------------------------------------------------
# GLOBAL ENV VARIABLES
# ------------------------------------------------------------
env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}   # owner/repo
  NODE_VERSION: 18

  # AWS / EKS
  AWS_REGION: ap-south-1
  EKS_CLUSTER_NAME: my-eks-cluster
  HELM_RELEASE_NAME: my-backend
  HELM_CHART_PATH: ./helm/backend

jobs:
  build-and-deploy:
    name: Build, Push & Deploy
    runs-on: ubuntu-latest

    steps:
      # --------------------------------------------------------
      # 1. CHECKOUT CODE (WITH TAGS)
      # --------------------------------------------------------
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      # --------------------------------------------------------
      # 2. SETUP NODE
      # --------------------------------------------------------
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      # --------------------------------------------------------
      # 3. INSTALL DEPENDENCIES (LOCKFILE SAFE)
      # --------------------------------------------------------
      - name: Install dependencies
        run: npm ci

      # --------------------------------------------------------
      # 4. CALCULATE NEXT SEMANTIC VERSION (PATCH)
      # --------------------------------------------------------
      - name: Calculate next version
        id: version
        run: |
          git fetch --tags

          LAST_TAG=$(git tag --sort=-v:refname | head -n 1)
          LAST_TAG=${LAST_TAG:-v0.0.0}

          echo "Last tag: $LAST_TAG"

          VERSION=${LAST_TAG#v}
          IFS='.' read -r MAJOR MINOR PATCH <<< "$VERSION"

          NEW_TAG="v$MAJOR.$MINOR.$((PATCH + 1))"

          echo "New tag: $NEW_TAG"
          echo "tag=$NEW_TAG" >> $GITHUB_OUTPUT

      # --------------------------------------------------------
      # 5. CREATE & PUSH GIT TAG (REVISION SOURCE OF TRUTH)
      # --------------------------------------------------------
      - name: Create git tag
        run: |
          git tag ${{ steps.version.outputs.tag }}
          git push origin ${{ steps.version.outputs.tag }}

      # --------------------------------------------------------
      # 6. LOGIN TO GHCR
      # --------------------------------------------------------
      - name: Login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      # --------------------------------------------------------
      # 7. BUILD DOCKER IMAGE
      # --------------------------------------------------------
      - name: Build Docker image
        run: |
          docker build \
            -t $REGISTRY/${{ env.IMAGE_NAME }}:${{ steps.version.outputs.tag }} \
            .

      # --------------------------------------------------------
      # 8. PUSH IMAGE TO GHCR
      # --------------------------------------------------------
      - name: Push Docker image
        run: |
          docker push $REGISTRY/${{ env.IMAGE_NAME }}:${{ steps.version.outputs.tag }}

      # --------------------------------------------------------
      # 9. CONFIGURE AWS CREDENTIALS
      # --------------------------------------------------------
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      # --------------------------------------------------------
      # 10. SETUP KUBECTL
      # --------------------------------------------------------
      - name: Setup kubectl
        uses: azure/setup-kubectl@v4

      # --------------------------------------------------------
      # 11. UPDATE KUBECONFIG FOR EKS
      # --------------------------------------------------------
      - name: Update kubeconfig
        run: |
          aws eks update-kubeconfig \
            --region $AWS_REGION \
            --name $EKS_CLUSTER_NAME

      # --------------------------------------------------------
      # 12. SETUP HELM
      # --------------------------------------------------------
      - name: Setup Helm
        uses: azure/setup-helm@v4

      # --------------------------------------------------------
      # 13. DEPLOY TO EKS USING HELM
      # --------------------------------------------------------
      - name: Deploy using Helm
        run: |
          helm upgrade --install $HELM_RELEASE_NAME $HELM_CHART_PATH \
            --set image.repository=$REGISTRY/${{ env.IMAGE_NAME }} \
            --set image.tag=${{ steps.version.outputs.tag }} \
            --atomic \
            --timeout 5m
```
