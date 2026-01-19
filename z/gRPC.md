---
tags:
  - tech/dev/frame
  - type/concept
  - status/growing
description: gRPC
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[API 通信技术]] | [[后端开发 MOC]]

---


# gRPC 

## 基础概念

### 什么是 gRPC

gRPC（Google Remote Procedure Call）是由 Google 开发的现代化、高性能、开源的通用 RPC 框架。它使用 HTTP/2 作为传输协议，Protocol Buffers 作为接口定义语言和消息交换格式。

### 核心特性

- **高性能**：基于 HTTP/2 协议，支持多路复用、流控制、头部压缩
- **跨语言支持**：支持 C++、Java、Python、Go、Ruby、C#、Node.js、Android Java、Objective-C、PHP 等
- **强类型接口**：使用 Protocol Buffers 定义服务接口
- **流式通信**：支持客户端流、服务端流、双向流
- **可插拔设计**：支持认证、负载均衡、重试等

### 工作原理

```
Client Application                    Server Application
       |                                      |
   gRPC Stub  <------ HTTP/2 ------>  gRPC Server
       |         (Protocol Buffers)          |
  Application                        Service Implementation
    Logic                              Logic
```

### gRPC vs REST API

| 特性       | gRPC             | REST     |
| ---------- | ---------------- | -------- |
| 协议       | HTTP/2           | HTTP/1.1 |
| 数据格式   | Protocol Buffers | JSON/XML |
| 性能       | 高               | 中等     |
| 流式传输   | 支持             | 不支持   |
| 浏览器支持 | 需要代理         | 原生支持 |
| 人类可读性 | 低               | 高       |

## 使用指南

### 环境准备

**安装 gRPC（Node.js）：**

```bash
npm install @grpc/grpc-js @grpc/proto-loader
npm install -D grpc-tools
```

**安装 gRPC（Python）：**

```bash
pip install grpcio grpcio-tools
```

**安装 gRPC（Go）：**

```bash
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

### 定义服务接口

创建 `calculator.proto` 文件：

```protobuf
syntax = "proto3";

package calculator;

// 计算器服务
service Calculator {
  // 基本运算
  rpc Add (BinaryOperationRequest) returns (CalculationResult);
  rpc Subtract (BinaryOperationRequest) returns (CalculationResult);
  rpc Multiply (BinaryOperationRequest) returns (CalculationResult);
  rpc Divide (BinaryOperationRequest) returns (CalculationResult);
  
  // 流式运算 - 服务端流
  rpc GetFibonacci (FibonacciRequest) returns (stream FibonacciResult);
  
  // 流式运算 - 客户端流
  rpc Sum (stream Number) returns (SumResult);
  
  // 双向流
  rpc Calculate (stream CalculationRequest) returns (stream CalculationResponse);
}

message BinaryOperationRequest {
  double a = 1;
  double b = 2;
}

message CalculationResult {
  double result = 1;
  bool success = 2;
  string error_message = 3;
}

message FibonacciRequest {
  int32 count = 1;
}

message FibonacciResult {
  int64 number = 1;
}

message Number {
  double value = 1;
}

message SumResult {
  double total = 1;
}

message CalculationRequest {
  enum Operation {
    ADD = 0;
    SUBTRACT = 1;
    MULTIPLY = 2;
    DIVIDE = 3;
  }
  
  Operation operation = 1;
  double a = 2;
  double b = 3;
}

message CalculationResponse {
  double result = 1;
  bool success = 2;
  string message = 3;
}
```

### 生成代码

**Node.js：**

```bash
npx grpc_tools_node_protoc \
  --js_out=import_style=commonjs,binary:./generated \
  --grpc_out=grpc_js:./generated \
  --plugin=protoc-gen-grpc=./node_modules/.bin/grpc_tools_node_protoc_plugin \
  -I ./protos \
  ./protos/calculator.proto
```

**Python：**

```bash
python -m grpc_tools.protoc \
  --proto_path=./protos \
  --python_out=./generated \
  --grpc_python_out=./generated \
  ./protos/calculator.proto
```

### 服务端实现

**Node.js 服务端：**

```javascript
const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');
const path = require('path');

// 加载 proto 文件
const PROTO_PATH = path.join(__dirname, 'protos/calculator.proto');
const packageDefinition = protoLoader.loadSync(PROTO_PATH, {
  keepCase: true,
  longs: String,
  enums: String,
  defaults: true,
  oneofs: true
});

const calculatorProto = grpc.loadPackageDefinition(packageDefinition).calculator;

// 服务实现
const calculatorService = {
  // 基本运算
  Add: (call, callback) => {
    const { a, b } = call.request;
    const result = a + b;
    callback(null, { result, success: true, error_message: '' });
  },

  Subtract: (call, callback) => {
    const { a, b } = call.request;
    const result = a - b;
    callback(null, { result, success: true, error_message: '' });
  },

  Multiply: (call, callback) => {
    const { a, b } = call.request;
    const result = a * b;
    callback(null, { result, success: true, error_message: '' });
  },

  Divide: (call, callback) => {
    const { a, b } = call.request;
    if (b === 0) {
      callback(null, { 
        result: 0, 
        success: false, 
        error_message: 'Division by zero' 
      });
      return;
    }
    const result = a / b;
    callback(null, { result, success: true, error_message: '' });
  },

  // 服务端流 - 生成斐波那契数列
  GetFibonacci: (call) => {
    const count = call.request.count;
    let a = 0, b = 1;
    
    for (let i = 0; i < count; i++) {
      call.write({ number: a });
      [a, b] = [b, a + b];
    }
    call.end();
  },

  // 客户端流 - 求和
  Sum: (call, callback) => {
    let total = 0;
    
    call.on('data', (request) => {
      total += request.value;
    });
    
    call.on('end', () => {
      callback(null, { total });
    });
  },

  // 双向流 - 实时计算
  Calculate: (call) => {
    call.on('data', (request) => {
      const { operation, a, b } = request;
      let result = 0;
      let success = true;
      let message = '';

      switch (operation) {
        case 0: // ADD
          result = a + b;
          break;
        case 1: // SUBTRACT
          result = a - b;
          break;
        case 2: // MULTIPLY
          result = a * b;
          break;
        case 3: // DIVIDE
          if (b === 0) {
            success = false;
            message = 'Division by zero';
          } else {
            result = a / b;
          }
          break;
      }

      call.write({ result, success, message });
    });

    call.on('end', () => {
      call.end();
    });
  }
};

// 创建并启动服务器
function startServer() {
  const server = new grpc.Server();
  
  server.addService(calculatorProto.Calculator.service, calculatorService);
  
  const address = '0.0.0.0:50051';
  server.bindAsync(address, grpc.ServerCredentials.createInsecure(), (err, port) => {
    if (err) {
      console.error('Server failed to bind:', err);
      return;
    }
    console.log(`Calculator server running at ${address}`);
    server.start();
  });
}

startServer();
```

### 客户端实现

**Node.js 客户端：**

```javascript
const grpc = require('@grpc/grpc-js');
const protoLoader = require('@grpc/proto-loader');
const path = require('path');

// 加载 proto 文件
const PROTO_PATH = path.join(__dirname, 'protos/calculator.proto');
const packageDefinition = protoLoader.loadSync(PROTO_PATH);
const calculatorProto = grpc.loadPackageDefinition(packageDefinition).calculator;

// 创建客户端
const client = new calculatorProto.Calculator(
  'localhost:50051',
  grpc.credentials.createInsecure()
);

// 基本运算示例
function basicOperations() {
  // 加法
  client.Add({ a: 10, b: 5 }, (err, response) => {
    if (err) {
      console.error('Add error:', err);
      return;
    }
    console.log('Add result:', response.result); // 15
  });

  // 除法（包含错误处理）
  client.Divide({ a: 10, b: 0 }, (err, response) => {
    if (err) {
      console.error('Divide error:', err);
      return;
    }
    if (!response.success) {
      console.log('Divide error:', response.error_message);
    } else {
      console.log('Divide result:', response.result);
    }
  });
}

// 服务端流示例
function serverStreamExample() {
  const call = client.GetFibonacci({ count: 10 });
  
  call.on('data', (response) => {
    console.log('Fibonacci number:', response.number);
  });
  
  call.on('end', () => {
    console.log('Fibonacci sequence completed');
  });
  
  call.on('error', (err) => {
    console.error('Stream error:', err);
  });
}

// 客户端流示例
function clientStreamExample() {
  const call = client.Sum((err, response) => {
    if (err) {
      console.error('Sum error:', err);
      return;
    }
    console.log('Sum result:', response.total);
  });

  // 发送多个数字
  [1, 2, 3, 4, 5].forEach(value => {
    call.write({ value });
  });
  
  call.end();
}

// 双向流示例
function bidirectionalStreamExample() {
  const call = client.Calculate();
  
  call.on('data', (response) => {
    console.log('Calculation result:', response);
  });
  
  call.on('end', () => {
    console.log('Bidirectional stream ended');
  });
  
  // 发送计算请求
  call.write({ operation: 0, a: 10, b: 5 }); // ADD
  call.write({ operation: 2, a: 3, b: 4 });  // MULTIPLY
  call.write({ operation: 3, a: 10, b: 2 }); // DIVIDE
  
  setTimeout(() => {
    call.end();
  }, 1000);
}

// 运行示例
basicOperations();
setTimeout(serverStreamExample, 1000);
setTimeout(clientStreamExample, 2000);
setTimeout(bidirectionalStreamExample, 3000);
```

## 实战经验

### 错误处理最佳实践

```javascript
// 服务端错误处理
const grpc = require('@grpc/grpc-js');

function handleDivision(call, callback) {
  const { a, b } = call.request;
  
  // 参数验证
  if (typeof a !== 'number' || typeof b !== 'number') {
    const error = new Error('Invalid arguments: a and b must be numbers');
    error.code = grpc.status.INVALID_ARGUMENT;
    callback(error);
    return;
  }
  
  // 业务逻辑验证
  if (b === 0) {
    const error = new Error('Division by zero is not allowed');
    error.code = grpc.status.INVALID_ARGUMENT;
    callback(error);
    return;
  }
  
  try {
    const result = a / b;
    callback(null, { result, success: true });
  } catch (err) {
    const error = new Error('Internal calculation error');
    error.code = grpc.status.INTERNAL;
    callback(error);
  }
}

// 客户端重试机制
function callWithRetry(client, method, request, maxRetries = 3) {
  return new Promise((resolve, reject) => {
    let attempts = 0;
    
    function attempt() {
      attempts++;
      client[method](request, (err, response) => {
        if (err) {
          if (attempts < maxRetries && shouldRetry(err)) {
            console.log(`Attempt ${attempts} failed, retrying...`);
            setTimeout(attempt, Math.pow(2, attempts) * 1000); // 指数退避
            return;
          }
          reject(err);
        } else {
          resolve(response);
        }
      });
    }
    
    attempt();
  });
}

function shouldRetry(error) {
  return [
    grpc.status.UNAVAILABLE,
    grpc.status.DEADLINE_EXCEEDED,
    grpc.status.RESOURCE_EXHAUSTED
  ].includes(error.code);
}
```

### 拦截器使用

```javascript
// 服务端拦截器
function loggingInterceptor(call, methodDefinition, next) {
  console.log(`[${new Date().toISOString()}] ${methodDefinition.path} called`);
  
  const startTime = Date.now();
  
  next(call, (err, result) => {
    const duration = Date.now() - startTime;
    console.log(`[${new Date().toISOString()}] ${methodDefinition.path} completed in ${duration}ms`);
    
    if (err) {
      console.error(`Error: ${err.message}`);
    }
  });
}

// 客户端拦截器
function authInterceptor(options, nextCall) {
  return new grpc.InterceptingCall(nextCall(options), {
    start: function(metadata, listener, next) {
      // 添加认证信息
      metadata.add('authorization', 'Bearer your-token-here');
      next(metadata, listener);
    }
  });
}

// 使用拦截器
const server = new grpc.Server();
server.addService(calculatorProto.Calculator.service, calculatorService, {
  interceptors: [loggingInterceptor]
});

const client = new calculatorProto.Calculator('localhost:50051', 
  grpc.credentials.createInsecure(), {
    interceptors: [authInterceptor]
  });
```

### 性能优化

```javascript
// 连接池配置
const client = new calculatorProto.Calculator('localhost:50051', 
  grpc.credentials.createInsecure(), {
    // 保持连接活跃
    'grpc.keepalive_time_ms': 30000,
    'grpc.keepalive_timeout_ms': 5000,
    'grpc.keepalive_permit_without_calls': true,
    
    // 连接参数
    'grpc.max_concurrent_streams': 100,
    'grpc.max_message_length': 4 * 1024 * 1024, // 4MB
    
    // 重试配置
    'grpc.enable_retries': true,
    'grpc.per_rpc_retry_buffer_size': 64 * 1024 * 1024 // 64MB
  });

// 启用压缩
client.Add({ a: 1, b: 2 }, { 
  compression: grpc.compression.gzip 
}, callback);
```

### 健康检查

```javascript
// 实现健康检查服务
const healthService = {
  Check: (call, callback) => {
    // 检查服务状态
    const isHealthy = checkServiceHealth();
    
    if (isHealthy) {
      callback(null, { status: 'SERVING' });
    } else {
      callback(null, { status: 'NOT_SERVING' });
    }
  },

  Watch: (call) => {
    // 流式健康检查
    const interval = setInterval(() => {
      const isHealthy = checkServiceHealth();
      call.write({ 
        status: isHealthy ? 'SERVING' : 'NOT_SERVING' 
      });
    }, 5000);

    call.on('cancelled', () => {
      clearInterval(interval);
    });
  }
};

function checkServiceHealth() {
  // 实现具体的健康检查逻辑
  // 例如：数据库连接、外部服务可用性等
  return true;
}
```

## 经验总结

### 优势

- **高性能**：基于 HTTP/2，支持多路复用和流控制
- **强类型**：Protocol Buffers 提供类型安全
- **跨语言**：多语言支持，便于微服务架构
- **流式传输**：支持实时数据传输
- **可扩展**：插件化架构，支持中间件

### 局限性

- **浏览器兼容性**：需要 gRPC-Web 代理支持
- **调试复杂**：二进制协议不如 JSON 直观
- **学习成本**：需要学习 Protocol Buffers
- **网络限制**：某些网络环境可能限制 HTTP/2

### 适用场景

**适合使用 gRPC：**

- 微服务内部通信
- 高性能要求的系统
- 多语言环境
- 需要流式传输的场景
- 强类型接口要求

**不适合使用 gRPC：**

- 直接浏览器访问（除非使用 gRPC-Web）
- 简单的 CRUD 操作
- 需要人类可读格式的 API
- 快速原型开发

### 最佳实践建议

1. **接口设计**：保持向后兼容，合理使用字段编号
2. **错误处理**：使用标准 gRPC 状态码
3. **性能优化**：启用压缩，合理配置连接参数
4. **监控日志**：实现完善的日志和监控
5. **测试策略**：编写单元测试和集成测试

## 信息参考

- [gRPC 官方网站](https://grpc.io/)
- [gRPC 官方文档](https://grpc.io/docs/)
- [Protocol Buffers 指南](https://developers.google.com/protocol-buffers)
- [gRPC Node.js API](https://grpc.github.io/grpc/node/)
- [gRPC Python API](https://grpc.github.io/grpc/python/)
- [gRPC Go 教程](https://grpc.io/docs/languages/go/)
- [gRPC Best Practices](https://grpc.io/docs/guides/best-practices/)
- [gRPC 性能优化指南](https://grpc.io/docs/guides/performance/)
- [Awesome gRPC](https://github.com/grpc-ecosystem/awesome-grpc)

