# --- Stage 1: Build ---
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app

# 1. Chỉ copy các file cấu hình Maven trước (để tận dụng Cache của Docker)
COPY .mvn/ .mvn
COPY mvnw pom.xml ./

# 2. Cấp quyền thực thi (quan trọng)
RUN chmod +x mvnw

# 3. Tải toàn bộ thư viện về trước (Dependency Resolution)
# Bước này giúp các lần build sau cực nhanh nếu bạn chỉ sửa code mà không sửa pom.xml
RUN ./mvnw dependency:go-offline

# 4. Giờ mới copy source code vào
COPY src ./src

# 5. Build code
RUN ./mvnw clean package -DskipTests

# --- Stage 2: Run ---
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Copy file .jar từ Stage 1
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
