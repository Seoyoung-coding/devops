upstream spring-backend {
    server api1:8080 max_fails=3 fail_timeout=30s;
    server api2:8080 max_fails=3 fail_timeout=30s;
    server api3:8080 max_fails=3 fail_timeout=30s;
}

// 3번실패하면 뺀다

aws console 사이트 -> 루트 사용자 로그인 -> 사용자 생성 -> 사용자는 다음 로그인시 ... (관리자라면) -> IAM 사용자
