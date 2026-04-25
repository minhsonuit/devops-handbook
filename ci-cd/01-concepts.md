# CI/CD Concepts

> Ngay bat dau: ___

## CI vs CD

```
CI (Continuous Integration)
  Code → Build → Test → Merge
  Muc tieu: phat hien loi som, merge thuong xuyen

CD (Continuous Delivery)
  Merge → Build artifact → Deploy to staging → Manual approve → Production
  Muc tieu: luon san sang deploy

CD (Continuous Deployment)
  Merge → Build → Test → Auto deploy to production
  Muc tieu: tu dong hoa hoan toan
```

## Pipeline stages

```
1. Source     → Pull code tu Git
2. Build      → Compile, build Docker image
3. Test       → Unit test, integration test
4. Scan       → Security scan, code quality
5. Package    → Push image to registry
6. Deploy     → Deploy to environment
7. Verify     → Health check, smoke test
```

## Best practices

- Moi commit trigger pipeline
- Fast feedback: build + test < 10 phut
- Khong commit truc tiep vao main
- Environment variables cho secrets
- Immutable artifacts (tag image bang commit SHA)
- Rollback phai nhanh va de
