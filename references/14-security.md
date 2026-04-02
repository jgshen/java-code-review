# 安全规约

## SE-001: 密码加密存储
**规则**：用户密码必须加密存储，禁止明文保存
**严重程度**：严重
**检查逻辑**：用户密码以明文形式存储
**正确示例**：
```java
// 使用BCrypt加密
String hashedPassword = BCrypt.hashpw(rawPassword, BCrypt.gensalt());

// 验证密码
if (BCrypt.checkpw(inputPassword, storedPassword)) {
    // 验证通过
}
```
**错误示例**：
```java
// 明文存储
user.setPassword(password);

// 或简单MD5/SHA1
user.setPassword(MD5(password));  // 不安全
```
**修复建议**：使用BCrypt、PBKDF2等强哈希算法

---

## SE-002: 敏感数据不打印日志
**规则**：敏感数据（密码、Token、身份证、手机号等）禁止打印到日志
**严重程度**：严重
**检查逻辑**：日志中包含敏感关键词
**正确示例**：
```java
log.info("User login: name={}, maskedPassword=***", username);
log.info("Payment: amount={}, cardNo=***", amount);
```
**错误示例**：
```java
log.info("User login: password={}", password);
log.info("Token: {}", token);
log.info("ID card: {}", idCard);
```
**修复建议**：脱敏后打印或使用***代替

---

## SE-003: SQL参数绑定
**规则**：禁止字符串拼接SQL，必须使用参数绑定
**严重程度**：严重
**检查逻辑**：使用+拼接SQL参数
**正确示例**：
```java
@Query("SELECT * FROM user WHERE name = ?1")
User findByName(String name);

PreparedStatement ps = conn.prepareStatement(
    "SELECT * FROM user WHERE name = ?"
);
ps.setString(1, name);
```
**错误示例**：
```java
@Query("SELECT * FROM user WHERE name = '" + name + "'")
User findByName(String name);

String sql = "SELECT * FROM user WHERE name = '" + name + "'";
```
**修复建议**：使用参数绑定防止SQL注入

---

## SE-004: 文件上传验证
**规则**：文件上传必须验证文件类型、大小和内容
**严重程度**：严重
**检查逻辑**：文件上传缺少验证
**正确示例**：
```java
public String upload(MultipartFile file) {
    // 验证文件大小
    if (file.getSize() > MAX_FILE_SIZE) {
        throw new IllegalArgumentException("File too large");
    }

    // 验证文件类型
    String ext = FilenameUtils.getExtension(file.getOriginalFilename());
    if (!ALLOWED_EXTENSIONS.contains(ext)) {
        throw new IllegalArgumentException("File type not allowed");
    }

    // 验证文件内容（MIME类型）
    if (!file.getContentType().startsWith("image/")) {
        throw new IllegalArgumentException("Only image allowed");
    }

    // 保存文件
}
```
**错误示例**：
```java
public String upload(MultipartFile file) {
    // 无任何验证
    file.transferTo(new File(uploadDir + file.getOriginalFilename()));
}
```
**修复建议**：验证文件大小、类型和内容

---

## SE-005: XSS防护
**规则**：用户输入必须进行XSS过滤
**严重程度**：严重
**检查逻辑**：用户输入未经过滤直接输出
**正确示例**：
```java
// 使用工具类过滤
String safeContent = HtmlUtils.htmlEscape(userInput);

// 或使用OWASP Java HTML Sanitizer
String safe = Sanitizers.FORMATTING.escape(userInput);
```
**错误示例**：
```java
// 直接输出用户输入
model.addAttribute("content", userInput);  // 可能导致XSS
```
**修复建议**：使用HTML转义或OWASP Sanitizers

---

## SE-006: CSRF防护
**规则**：必须开启CSRF防护
**严重程度**：严重
**检查逻辑**：未开启CSRF防护
**正确示例**：
```java
// Spring Security配置
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            );
        return http.build();
    }
}
```
**错误示例**：
```java
// 禁用CSRF
http.csrf().disable();
```
**修复建议**：除非有特殊原因，否则必须开启CSRF防护

---

## SE-007: 权限校验
**规则**：必须对每个请求进行权限校验
**严重程度**：严重
**检查逻辑**：缺少权限校验
**正确示例**：
```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) {
    // 检查当前用户是否有权限
    if (!currentUser.isAdmin()) {
        throw new AccessDeniedException("No permission");
    }
    // 业务逻辑
}
```
**错误示例**：
```java
public void deleteUser(Long id) {
    // 无权限校验
    userRepository.deleteById(id);
}
```
**修复建议**：使用@PreAuthorize或手动校验权限

---

## SE-008: 禁止明文密码传输
**规则**：禁止在HTTP请求中明文传输密码
**严重程度**：严重
**检查逻辑**：HTTP请求中包含明文密码
**正确示例**：
```java
// 必须使用HTTPS
https://api.example.com/login

// 使用加密传输
String encryptedPassword = RSAEncrypt(password);
```
**错误示例**：
```java
// HTTP明文传输
http://api.example.com/login?password=123456
```
**修复建议**：使用HTTPS，禁止明文密码传输

---

## SE-009: Session安全
**规则**：Session必须设置安全属性
**严重程度**：警告
**检查逻辑**：Session未设置安全属性
**正确示例**：
```java
// Session配置
session.setAttribute("Secure", true);
session.setAttribute("HttpOnly", true);
session.setAttribute("SameSite", "Strict");
```
**错误示例**：
```java
// 无任何安全配置
session.setAttribute("user", currentUser);
```
**修复建议**：设置Secure、HttpOnly、SameSite属性

---

## SE-010: 密钥硬编码
**规则**：禁止在代码中硬编码密钥
**严重程度**：严重
**检查逻辑**：代码中存在硬编码密钥
**正确示例**：
```java
// 从环境变量或配置中心获取
String apiKey = System.getenv("API_KEY");
String secret = vault.read("secret/api-secret");
```
**错误示例**：
```java
private static final String API_KEY = "abcdef123456";  // 硬编码
private static final String SECRET = "my-secret-key";
```
**修复建议**：从环境变量、配置中心或密钥管理系统获取

---

## SE-011: 错误信息泄露
**规则**：禁止在错误信息中泄露敏感信息
**严重程度**：警告
**检查逻辑**：错误信息包含敏感数据
**正确示例**：
```java
// 生产环境
log.error("Database error", e);
return "Error occurred";  // 对用户隐藏详情

// 开发环境
log.error("Database error", e);
throw new DetailedException(e);  // 仅对开发者显示
```
**错误示例**：
```java
// 对用户暴露内部信息
catch (SQLException e) {
    throw new Exception("Connection failed: " + e.getMessage() +
        ", URL: " + dbUrl +
        ", Username: " + dbUsername);
}
```
**修复建议**：生产环境隐藏详细错误信息

---

## SE-012: 禁止调试代码
**规则**：生产代码禁止包含调试代码
**严重程度**：警告
**检查逻辑**：存在System.out/printStackTrace等调试代码
**正确示例**：
```java
logger.info("Processing order: {}", orderId);
```
**错误示例**：
```java
System.out.println("Debug: " + variable);
e.printStackTrace();
```
**修复建议**：删除调试代码，使用日志框架
