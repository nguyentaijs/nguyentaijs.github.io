
# 1. Cấu trúc của Docker file

```
  # 1. Set os
  FROM openjdk:17-jdk-alpine

  # 2. Set working directory
  WORKDIR /app

  # 3. Copy Jar file
  ## Copy jar file from target -> internal container /app
  COPY target/app.jar app.jar

  # 4. Expose port app
  EXPOSE 8080

  # 5. Run java application
  ENTRYPOINT ["java", "-jar", "app.jar"]
```
