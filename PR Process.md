# Pull Requests and GitHub Flow

## Pull Request Concept

A **pull request (PR)** is a request to merge code from one branch into another. It enables code review, discussion, and quality checks before integration. Key benefits:
- **Code review**: Team members examine changes before merging
- **Discussion**: Questions and suggestions can be raised
- **CI/CD integration**: Automated tests run before approval
- **Audit trail**: Records who changed what and why

## GitHub Flow Overview

GitHub Flow is a simple branching strategy:
1. Create a feature branch from `main`
2. Make commits and push to the branch
3. Open a PR for review
4. Discuss and iterate
5. Merge to `main` once approved
6. Deploy from `main`

## Code Promotion: Dev → QA → Staging → Production

Here's how code typically moves through environments:

```
Feature Branch → Pull Request → main branch → Deploy to QA
                                    ↓
                              QA Testing & Approval
                                    ↓
                    Create Release Branch (or tag)
                                    ↓
                            Deploy to Staging
                                    ↓
                         Staging Testing & Approval
                                    ↓
                      Merge Release to Production
                                    ↓
                         Deploy to Production
```

### Practical Workflow

1. **Development → QA**
   - Developer creates PR from feature branch to `main`
   - After approval and merge, `main` is automatically deployed to QA environment
   - QA team tests the changes

2. **QA → Staging**
   - Once QA approves, create a release branch (e.g., `release/v1.2.0`) from `main`
   - Deploy release branch to Staging for final validation
   - This is often tagged in git for version control

3. **Staging → Production**
   - After Staging approval, merge release branch to a `production` branch (or directly deploy the tag)
   - Trigger production deployment
   - Tag the commit with version number for tracking

### Key Practices

- **Branch protection rules**: Require PR reviews, passing checks before merge
- **Automated CI/CD**: Run tests, linting, builds automatically on PR
- **Environment-specific configs**: Use different configurations per environment
- **Rollback strategy**: Keep previous versions deployable if issues arise
- **Communication**: Notify team at each stage transition

This ensures code is thoroughly reviewed and tested before reaching production.
