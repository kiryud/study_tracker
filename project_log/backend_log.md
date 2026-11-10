# backend log

## temp user service api

- user (data class)
    - seq (PK)
    - UUIDv7 
    - id (Unique)
    - nick
    - pw

- signup api
    - POST
        - id
        - nick
        - pw
    - Success
        - UUIDv7
        - nick
    - Fail
        - 이미 가입된 계정

- login api
    - POST
        - id
        - pw
    - Success
        - UUIDv7
        - nick
    - Fail
        - 불일치


- 뭐든 임시 api 서버 만드는건 쉬우니 대충 아무거나 필요할때 써보는걸로

```py
from dataclasses import dataclass
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from uuid6 import uuid7
app = FastAPI(title="Temp User Service API")
# 1. Data Class (Entity & DTO)
@dataclass
class User:
    seq: int
    uuid: str
    id: str
    nick: str
    pw: str
class SignupRequest(BaseModel):
    id: str
    nick: str
    pw: str
class LoginRequest(BaseModel):
    id: str
    pw: str
class AuthResponse(BaseModel):
    uuid: str
    nick: str
# 인메모리 DB (key: user.id)
users_db: dict[str, User] = {}
seq_counter: int = 0
# 2. Signup API
@app.post("/api/v1/auth/signup", response_model=AuthResponse, status_code=201)
def signup(req: SignupRequest):
    global seq_counter
    if req.id in users_db:
        raise HTTPException(status_code=409, detail="이미 가입된 계정")
    seq_counter += 1
    new_user = User(
        seq=seq_counter,
        uuid=str(uuid7()),
        id=req.id,
        nick=req.nick,
        pw=req.pw,
    )
    users_db[req.id] = new_user
    return AuthResponse(uuid=new_user.uuid, nick=new_user.nick)
# 3. Login API
@app.post("/api/v1/auth/login", response_model=AuthResponse)
def login(req: LoginRequest):
    user = users_db.get(req.id)
    if user is None or user.pw != req.pw:
        raise HTTPException(status_code=401, detail="불일치")
    return AuthResponse(uuid=user.uuid, nick=user.nick)
```

```c#
using System.Collections.Concurrent;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// 인메모리 DB (key: user.Id)
var usersDb = new ConcurrentDictionary<string, User>();
var seqCounter = 0;

// 2. Signup API
app.MapPost("/api/v1/auth/signup", (SignupRequest req) =>
{
    if (usersDb.ContainsKey(req.Id))
        return Results.Conflict(new { detail = "이미 가입된 계정" });

    var seq = Interlocked.Increment(ref seqCounter);
    var newUser = new User(
        Seq: seq,
        Uuid: Guid.CreateVersion7().ToString(),
        Id: req.Id,
        Nick: req.Nick,
        Pw: req.Pw
    );

    usersDb[req.Id] = newUser;
    return Results.Created($"/api/v1/users/{newUser.Uuid}", new AuthResponse(newUser.Uuid, newUser.Nick));
});

// 3. Login API
app.MapPost("/api/v1/auth/login", (LoginRequest req) =>
{
    if (!usersDb.TryGetValue(req.Id, out var user) || user.Pw != req.Pw)
        return Results.Json(new { detail = "불일치" }, statusCode: 401);

    return Results.Ok(new AuthResponse(user.Uuid, user.Nick));
});

app.Run("http://0.0.0.0:8080");

// 1. Data Classes (Record)
record User(int Seq, string Uuid, string Id, string Nick, string Pw);
record SignupRequest(string Id, string Nick, string Pw);
record LoginRequest(string Id, string Pw);
record AuthResponse(string Uuid, string Nick);
```
