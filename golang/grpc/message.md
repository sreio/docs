## 字段类型关键字

- `required`: 该字段是必需的，在消息中必须出现，而且只能出现一次。但是在Protocol Buffers 3中已经被弃用，不推荐使用，因为它引入了一些复杂性，并且不够灵活。v3 中，字段修饰符是可选的，并且默认为可选。
- `optional`: 该字段是可选的，可以出现零次或一次。这是默认的行为，如果没有显式指定字段的修饰符，默认为可选。
- `repeated`: 该字段可以包含零个或多个值，类似于列表或数组。可以在同一消息中重复多次。

> 演示文件目录可以参考如下：

<details>
<summary>文件目录</summary>

- grpc_study
  - grpc_message
    - proto // protocbuf 文件
      - user.proto
      - any.proto
    - user  // protoc 生成的文件
      - user.pb.go
    - main.go

</details>

<details>
<summary>any.proto(文件内容是从https://github.com/google/protobuf/releases安装包内复制的)</summary>

```protobuf
// Protocol Buffers - Google's data interchange format
// Copyright 2008 Google Inc.  All rights reserved.
// https://developers.google.com/protocol-buffers/
//
// Redistribution and use in source and binary forms, with or without
// modification, are permitted provided that the following conditions are
// met:
//
//     * Redistributions of source code must retain the above copyright
// notice, this list of conditions and the following disclaimer.
//     * Redistributions in binary form must reproduce the above
// copyright notice, this list of conditions and the following disclaimer
// in the documentation and/or other materials provided with the
// distribution.
//     * Neither the name of Google Inc. nor the names of its
// contributors may be used to endorse or promote products derived from
// this software without specific prior written permission.
//
// THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
// "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
// LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
// A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
// OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
// SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
// LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
// DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
// THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
// (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
// OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

syntax = "proto3";

package google.protobuf;

option go_package = "google.golang.org/protobuf/types/known/anypb";
option java_package = "com.google.protobuf";
option java_outer_classname = "AnyProto";
option java_multiple_files = true;
option objc_class_prefix = "GPB";
option csharp_namespace = "Google.Protobuf.WellKnownTypes";

// `Any` contains an arbitrary serialized protocol buffer message along with a
// URL that describes the type of the serialized message.
//
// Protobuf library provides support to pack/unpack Any values in the form
// of utility functions or additional generated methods of the Any type.
//
// Example 1: Pack and unpack a message in C++.
//
//     Foo foo = ...;
//     Any any;
//     any.PackFrom(foo);
//     ...
//     if (any.UnpackTo(&foo)) {
//       ...
//     }
//
// Example 2: Pack and unpack a message in Java.
//
//     Foo foo = ...;
//     Any any = Any.pack(foo);
//     ...
//     if (any.is(Foo.class)) {
//       foo = any.unpack(Foo.class);
//     }
//     // or ...
//     if (any.isSameTypeAs(Foo.getDefaultInstance())) {
//       foo = any.unpack(Foo.getDefaultInstance());
//     }
//
//  Example 3: Pack and unpack a message in Python.
//
//     foo = Foo(...)
//     any = Any()
//     any.Pack(foo)
//     ...
//     if any.Is(Foo.DESCRIPTOR):
//       any.Unpack(foo)
//       ...
//
//  Example 4: Pack and unpack a message in Go
//
//      foo := &pb.Foo{...}
//      any, err := anypb.New(foo)
//      if err != nil {
//        ...
//      }
//      ...
//      foo := &pb.Foo{}
//      if err := any.UnmarshalTo(foo); err != nil {
//        ...
//      }
//
// The pack methods provided by protobuf library will by default use
// 'type.googleapis.com/full.type.name' as the type URL and the unpack
// methods only use the fully qualified type name after the last '/'
// in the type URL, for example "foo.bar.com/x/y.z" will yield type
// name "y.z".
//
// JSON
// ====
// The JSON representation of an `Any` value uses the regular
// representation of the deserialized, embedded message, with an
// additional field `@type` which contains the type URL. Example:
//
//     package google.profile;
//     message Person {
//       string first_name = 1;
//       string last_name = 2;
//     }
//
//     {
//       "@type": "type.googleapis.com/google.profile.Person",
//       "firstName": <string>,
//       "lastName": <string>
//     }
//
// If the embedded message type is well-known and has a custom JSON
// representation, that representation will be embedded adding a field
// `value` which holds the custom JSON in addition to the `@type`
// field. Example (for message [google.protobuf.Duration][]):
//
//     {
//       "@type": "type.googleapis.com/google.protobuf.Duration",
//       "value": "1.212s"
//     }
//
message Any {
  // A URL/resource name that uniquely identifies the type of the serialized
  // protocol buffer message. This string must contain at least
  // one "/" character. The last segment of the URL's path must represent
  // the fully qualified name of the type (as in
  // `path/google.protobuf.Duration`). The name should be in a canonical form
  // (e.g., leading "." is not accepted).
  //
  // In practice, teams usually precompile into the binary all types that they
  // expect it to use in the context of Any. However, for URLs which use the
  // scheme `http`, `https`, or no scheme, one can optionally set up a type
  // server that maps type URLs to message definitions as follows:
  //
  // * If no scheme is provided, `https` is assumed.
  // * An HTTP GET on the URL must yield a [google.protobuf.Type][]
  //   value in binary format, or produce an error.
  // * Applications are allowed to cache lookup results based on the
  //   URL, or have them precompiled into a binary to avoid any
  //   lookup. Therefore, binary compatibility needs to be preserved
  //   on changes to types. (Use versioned type names to manage
  //   breaking changes.)
  //
  // Note: this functionality is not currently available in the official
  // protobuf release, and it is not used for type URLs beginning with
  // type.googleapis.com. As of May 2023, there are no widely used type server
  // implementations and no plans to implement one.
  //
  // Schemes other than `http`, `https` (or the empty scheme) might be
  // used with implementation specific semantics.
  //
  string type_url = 1;

  // Must be a valid serialized protocol buffer of the above specified type.
  bytes value = 2;
}

```

</details>


#### grpc_study/grpc_message/proto/user.proto
```protobuf 
syntax = "proto3";

import "any.proto";

option go_package = "../user;User";

package User;

message User {
  string name = 1;
  int32 age = 2;
  bool isShow = 3;
  float height = 4;
  repeated string hobbies = 5; // 重复使用 类似于golang中的字符串切片 等于 []string
  optional string address = 6; // 可选字段
  // 嵌套
  message Time {
    int64 created_at = 1;
    int64 updated_at = 2;
  }
  Time time = 8;
  // 枚举类型
  enum Gender {
    MALE = 0;
    FEMALE = 1;
  }
  Gender gender = 9;
  // 枚举类型 - 预留值
  enum Type {
    UNKNOWN = 0;
    TYPE1 = 1;
    TYPE3 = 3;
    // 添加更多类型
    reserved 2, 15, 9 to 11, 40 to max;
    reserved "FOO", "BAR";
  }
  Type type = 10;

  // 导入其他proto文件里的类型
  google.protobuf.Any any = 11;
}

// 使用其他消息类型并且复用
message UserList {
  repeated User users = 1;
}

```

#### 执行protoc命令生成相对应的文件

```bash
#当前命令执行路径：grpc_study\grpc_message\proto
protoc --go_out=. --go-grpc_out=. .\user.proto
```

- `--go_out`: 指定生成的文件输出路径, 最终按照`go_package`指定的路径、包名生成在相对应的目录下文件、包名，所以这里随便填写一个路径即可
- `--go-grpc_out`: 指定生成的文件输出路径, 最终按照`go_package`指定的路径、包名生成在相对应的目录下文件、包名，所以这里随便填写一个路径即可
- `.\proto\user.proto`：指定要生成的protobuf文件


<details>
<summary>grpc_study/grpc_message/user/user.pb.go 生成的文件内容如下(请不要更改)：</summary>

```go
// Code generated by protoc-gen-go. DO NOT EDIT.
// versions:
// 	protoc-gen-go v1.33.0
// 	protoc        v5.26.1
// source: user.proto

package User

import (
	protoreflect "google.golang.org/protobuf/reflect/protoreflect"
	protoimpl "google.golang.org/protobuf/runtime/protoimpl"
	anypb "google.golang.org/protobuf/types/known/anypb"
	reflect "reflect"
	sync "sync"
)

const (
	// Verify that this generated code is sufficiently up-to-date.
	_ = protoimpl.EnforceVersion(20 - protoimpl.MinVersion)
	// Verify that runtime/protoimpl is sufficiently up-to-date.
	_ = protoimpl.EnforceVersion(protoimpl.MaxVersion - 20)
)

// 枚举类型
type User_Gender int32

const (
	User_MALE   User_Gender = 0
	User_FEMALE User_Gender = 1
)

// Enum value maps for User_Gender.
var (
	User_Gender_name = map[int32]string{
		0: "MALE",
		1: "FEMALE",
	}
	User_Gender_value = map[string]int32{
		"MALE":   0,
		"FEMALE": 1,
	}
)

func (x User_Gender) Enum() *User_Gender {
	p := new(User_Gender)
	*p = x
	return p
}

func (x User_Gender) String() string {
	return protoimpl.X.EnumStringOf(x.Descriptor(), protoreflect.EnumNumber(x))
}

func (User_Gender) Descriptor() protoreflect.EnumDescriptor {
	return file_user_proto_enumTypes[0].Descriptor()
}

func (User_Gender) Type() protoreflect.EnumType {
	return &file_user_proto_enumTypes[0]
}

func (x User_Gender) Number() protoreflect.EnumNumber {
	return protoreflect.EnumNumber(x)
}

// Deprecated: Use User_Gender.Descriptor instead.
func (User_Gender) EnumDescriptor() ([]byte, []int) {
	return file_user_proto_rawDescGZIP(), []int{0, 0}
}

// 枚举类型 - 预留值
type User_Type int32

const (
	User_UNKNOWN User_Type = 0
	User_TYPE1   User_Type = 1
	User_TYPE3   User_Type = 3
)

// Enum value maps for User_Type.
var (
	User_Type_name = map[int32]string{
		0: "UNKNOWN",
		1: "TYPE1",
		3: "TYPE3",
	}
	User_Type_value = map[string]int32{
		"UNKNOWN": 0,
		"TYPE1":   1,
		"TYPE3":   3,
	}
)

func (x User_Type) Enum() *User_Type {
	p := new(User_Type)
	*p = x
	return p
}

func (x User_Type) String() string {
	return protoimpl.X.EnumStringOf(x.Descriptor(), protoreflect.EnumNumber(x))
}

func (User_Type) Descriptor() protoreflect.EnumDescriptor {
	return file_user_proto_enumTypes[1].Descriptor()
}

func (User_Type) Type() protoreflect.EnumType {
	return &file_user_proto_enumTypes[1]
}

func (x User_Type) Number() protoreflect.EnumNumber {
	return protoreflect.EnumNumber(x)
}

// Deprecated: Use User_Type.Descriptor instead.
func (User_Type) EnumDescriptor() ([]byte, []int) {
	return file_user_proto_rawDescGZIP(), []int{0, 1}
}

type User struct {
	state         protoimpl.MessageState
	sizeCache     protoimpl.SizeCache
	unknownFields protoimpl.UnknownFields

	Name    string      `protobuf:"bytes,1,opt,name=name,proto3" json:"name,omitempty"`
	Age     int32       `protobuf:"varint,2,opt,name=age,proto3" json:"age,omitempty"`
	IsShow  bool        `protobuf:"varint,3,opt,name=isShow,proto3" json:"isShow,omitempty"`
	Height  float32     `protobuf:"fixed32,4,opt,name=height,proto3" json:"height,omitempty"`
	Hobbies []string    `protobuf:"bytes,5,rep,name=hobbies,proto3" json:"hobbies,omitempty"`       // 重复使用 类似于golang中的字符串切片 等于 []string
	Address *string     `protobuf:"bytes,6,opt,name=address,proto3,oneof" json:"address,omitempty"` // 可选字段
	Time    *User_Time  `protobuf:"bytes,8,opt,name=time,proto3" json:"time,omitempty"`
	Gender  User_Gender `protobuf:"varint,9,opt,name=gender,proto3,enum=User.User_Gender" json:"gender,omitempty"`
	Type    User_Type   `protobuf:"varint,10,opt,name=type,proto3,enum=User.User_Type" json:"type,omitempty"`
	// 导入其他proto文件里的类型
	Any *anypb.Any `protobuf:"bytes,11,opt,name=any,proto3" json:"any,omitempty"`
}

func (x *User) Reset() {
	*x = User{}
	if protoimpl.UnsafeEnabled {
		mi := &file_user_proto_msgTypes[0]
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		ms.StoreMessageInfo(mi)
	}
}

func (x *User) String() string {
	return protoimpl.X.MessageStringOf(x)
}

func (*User) ProtoMessage() {}

func (x *User) ProtoReflect() protoreflect.Message {
	mi := &file_user_proto_msgTypes[0]
	if protoimpl.UnsafeEnabled && x != nil {
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		if ms.LoadMessageInfo() == nil {
			ms.StoreMessageInfo(mi)
		}
		return ms
	}
	return mi.MessageOf(x)
}

// Deprecated: Use User.ProtoReflect.Descriptor instead.
func (*User) Descriptor() ([]byte, []int) {
	return file_user_proto_rawDescGZIP(), []int{0}
}

func (x *User) GetName() string {
	if x != nil {
		return x.Name
	}
	return ""
}

func (x *User) GetAge() int32 {
	if x != nil {
		return x.Age
	}
	return 0
}

func (x *User) GetIsShow() bool {
	if x != nil {
		return x.IsShow
	}
	return false
}

func (x *User) GetHeight() float32 {
	if x != nil {
		return x.Height
	}
	return 0
}

func (x *User) GetHobbies() []string {
	if x != nil {
		return x.Hobbies
	}
	return nil
}

func (x *User) GetAddress() string {
	if x != nil && x.Address != nil {
		return *x.Address
	}
	return ""
}

func (x *User) GetTime() *User_Time {
	if x != nil {
		return x.Time
	}
	return nil
}

func (x *User) GetGender() User_Gender {
	if x != nil {
		return x.Gender
	}
	return User_MALE
}

func (x *User) GetType() User_Type {
	if x != nil {
		return x.Type
	}
	return User_UNKNOWN
}

func (x *User) GetAny() *anypb.Any {
	if x != nil {
		return x.Any
	}
	return nil
}

// 使用其他消息类型并且复用
type UserList struct {
	state         protoimpl.MessageState
	sizeCache     protoimpl.SizeCache
	unknownFields protoimpl.UnknownFields

	Users []*User `protobuf:"bytes,1,rep,name=users,proto3" json:"users,omitempty"`
}

func (x *UserList) Reset() {
	*x = UserList{}
	if protoimpl.UnsafeEnabled {
		mi := &file_user_proto_msgTypes[1]
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		ms.StoreMessageInfo(mi)
	}
}

func (x *UserList) String() string {
	return protoimpl.X.MessageStringOf(x)
}

func (*UserList) ProtoMessage() {}

func (x *UserList) ProtoReflect() protoreflect.Message {
	mi := &file_user_proto_msgTypes[1]
	if protoimpl.UnsafeEnabled && x != nil {
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		if ms.LoadMessageInfo() == nil {
			ms.StoreMessageInfo(mi)
		}
		return ms
	}
	return mi.MessageOf(x)
}

// Deprecated: Use UserList.ProtoReflect.Descriptor instead.
func (*UserList) Descriptor() ([]byte, []int) {
	return file_user_proto_rawDescGZIP(), []int{1}
}

func (x *UserList) GetUsers() []*User {
	if x != nil {
		return x.Users
	}
	return nil
}

// 嵌套
type User_Time struct {
	state         protoimpl.MessageState
	sizeCache     protoimpl.SizeCache
	unknownFields protoimpl.UnknownFields

	CreatedAt int64 `protobuf:"varint,1,opt,name=created_at,json=createdAt,proto3" json:"created_at,omitempty"`
	UpdatedAt int64 `protobuf:"varint,2,opt,name=updated_at,json=updatedAt,proto3" json:"updated_at,omitempty"`
}

func (x *User_Time) Reset() {
	*x = User_Time{}
	if protoimpl.UnsafeEnabled {
		mi := &file_user_proto_msgTypes[2]
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		ms.StoreMessageInfo(mi)
	}
}

func (x *User_Time) String() string {
	return protoimpl.X.MessageStringOf(x)
}

func (*User_Time) ProtoMessage() {}

func (x *User_Time) ProtoReflect() protoreflect.Message {
	mi := &file_user_proto_msgTypes[2]
	if protoimpl.UnsafeEnabled && x != nil {
		ms := protoimpl.X.MessageStateOf(protoimpl.Pointer(x))
		if ms.LoadMessageInfo() == nil {
			ms.StoreMessageInfo(mi)
		}
		return ms
	}
	return mi.MessageOf(x)
}

// Deprecated: Use User_Time.ProtoReflect.Descriptor instead.
func (*User_Time) Descriptor() ([]byte, []int) {
	return file_user_proto_rawDescGZIP(), []int{0, 0}
}

func (x *User_Time) GetCreatedAt() int64 {
	if x != nil {
		return x.CreatedAt
	}
	return 0
}

func (x *User_Time) GetUpdatedAt() int64 {
	if x != nil {
		return x.UpdatedAt
	}
	return 0
}

var File_user_proto protoreflect.FileDescriptor

var file_user_proto_rawDesc = []byte{
	0x0a, 0x0a, 0x75, 0x73, 0x65, 0x72, 0x2e, 0x70, 0x72, 0x6f, 0x74, 0x6f, 0x12, 0x04, 0x55, 0x73,
	0x65, 0x72, 0x1a, 0x09, 0x61, 0x6e, 0x79, 0x2e, 0x70, 0x72, 0x6f, 0x74, 0x6f, 0x22, 0xf5, 0x03,
	0x0a, 0x04, 0x55, 0x73, 0x65, 0x72, 0x12, 0x12, 0x0a, 0x04, 0x6e, 0x61, 0x6d, 0x65, 0x18, 0x01,
	0x20, 0x01, 0x28, 0x09, 0x52, 0x04, 0x6e, 0x61, 0x6d, 0x65, 0x12, 0x10, 0x0a, 0x03, 0x61, 0x67,
	0x65, 0x18, 0x02, 0x20, 0x01, 0x28, 0x05, 0x52, 0x03, 0x61, 0x67, 0x65, 0x12, 0x16, 0x0a, 0x06,
	0x69, 0x73, 0x53, 0x68, 0x6f, 0x77, 0x18, 0x03, 0x20, 0x01, 0x28, 0x08, 0x52, 0x06, 0x69, 0x73,
	0x53, 0x68, 0x6f, 0x77, 0x12, 0x16, 0x0a, 0x06, 0x68, 0x65, 0x69, 0x67, 0x68, 0x74, 0x18, 0x04,
	0x20, 0x01, 0x28, 0x02, 0x52, 0x06, 0x68, 0x65, 0x69, 0x67, 0x68, 0x74, 0x12, 0x18, 0x0a, 0x07,
	0x68, 0x6f, 0x62, 0x62, 0x69, 0x65, 0x73, 0x18, 0x05, 0x20, 0x03, 0x28, 0x09, 0x52, 0x07, 0x68,
	0x6f, 0x62, 0x62, 0x69, 0x65, 0x73, 0x12, 0x1d, 0x0a, 0x07, 0x61, 0x64, 0x64, 0x72, 0x65, 0x73,
	0x73, 0x18, 0x06, 0x20, 0x01, 0x28, 0x09, 0x48, 0x00, 0x52, 0x07, 0x61, 0x64, 0x64, 0x72, 0x65,
	0x73, 0x73, 0x88, 0x01, 0x01, 0x12, 0x23, 0x0a, 0x04, 0x74, 0x69, 0x6d, 0x65, 0x18, 0x08, 0x20,
	0x01, 0x28, 0x0b, 0x32, 0x0f, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e,
	0x54, 0x69, 0x6d, 0x65, 0x52, 0x04, 0x74, 0x69, 0x6d, 0x65, 0x12, 0x29, 0x0a, 0x06, 0x67, 0x65,
	0x6e, 0x64, 0x65, 0x72, 0x18, 0x09, 0x20, 0x01, 0x28, 0x0e, 0x32, 0x11, 0x2e, 0x55, 0x73, 0x65,
	0x72, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e, 0x47, 0x65, 0x6e, 0x64, 0x65, 0x72, 0x52, 0x06, 0x67,
	0x65, 0x6e, 0x64, 0x65, 0x72, 0x12, 0x23, 0x0a, 0x04, 0x74, 0x79, 0x70, 0x65, 0x18, 0x0a, 0x20,
	0x01, 0x28, 0x0e, 0x32, 0x0f, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e,
	0x54, 0x79, 0x70, 0x65, 0x52, 0x04, 0x74, 0x79, 0x70, 0x65, 0x12, 0x26, 0x0a, 0x03, 0x61, 0x6e,
	0x79, 0x18, 0x0b, 0x20, 0x01, 0x28, 0x0b, 0x32, 0x14, 0x2e, 0x67, 0x6f, 0x6f, 0x67, 0x6c, 0x65,
	0x2e, 0x70, 0x72, 0x6f, 0x74, 0x6f, 0x62, 0x75, 0x66, 0x2e, 0x41, 0x6e, 0x79, 0x52, 0x03, 0x61,
	0x6e, 0x79, 0x1a, 0x44, 0x0a, 0x04, 0x54, 0x69, 0x6d, 0x65, 0x12, 0x1d, 0x0a, 0x0a, 0x63, 0x72,
	0x65, 0x61, 0x74, 0x65, 0x64, 0x5f, 0x61, 0x74, 0x18, 0x01, 0x20, 0x01, 0x28, 0x03, 0x52, 0x09,
	0x63, 0x72, 0x65, 0x61, 0x74, 0x65, 0x64, 0x41, 0x74, 0x12, 0x1d, 0x0a, 0x0a, 0x75, 0x70, 0x64,
	0x61, 0x74, 0x65, 0x64, 0x5f, 0x61, 0x74, 0x18, 0x02, 0x20, 0x01, 0x28, 0x03, 0x52, 0x09, 0x75,
	0x70, 0x64, 0x61, 0x74, 0x65, 0x64, 0x41, 0x74, 0x22, 0x1e, 0x0a, 0x06, 0x47, 0x65, 0x6e, 0x64,
	0x65, 0x72, 0x12, 0x08, 0x0a, 0x04, 0x4d, 0x41, 0x4c, 0x45, 0x10, 0x00, 0x12, 0x0a, 0x0a, 0x06,
	0x46, 0x45, 0x4d, 0x41, 0x4c, 0x45, 0x10, 0x01, 0x22, 0x4f, 0x0a, 0x04, 0x54, 0x79, 0x70, 0x65,
	0x12, 0x0b, 0x0a, 0x07, 0x55, 0x4e, 0x4b, 0x4e, 0x4f, 0x57, 0x4e, 0x10, 0x00, 0x12, 0x09, 0x0a,
	0x05, 0x54, 0x59, 0x50, 0x45, 0x31, 0x10, 0x01, 0x12, 0x09, 0x0a, 0x05, 0x54, 0x59, 0x50, 0x45,
	0x33, 0x10, 0x03, 0x22, 0x04, 0x08, 0x02, 0x10, 0x02, 0x22, 0x04, 0x08, 0x0f, 0x10, 0x0f, 0x22,
	0x04, 0x08, 0x09, 0x10, 0x0b, 0x22, 0x08, 0x08, 0x28, 0x10, 0xff, 0xff, 0xff, 0xff, 0x07, 0x2a,
	0x03, 0x46, 0x4f, 0x4f, 0x2a, 0x03, 0x42, 0x41, 0x52, 0x42, 0x0a, 0x0a, 0x08, 0x5f, 0x61, 0x64,
	0x64, 0x72, 0x65, 0x73, 0x73, 0x22, 0x2c, 0x0a, 0x08, 0x55, 0x73, 0x65, 0x72, 0x4c, 0x69, 0x73,
	0x74, 0x12, 0x20, 0x0a, 0x05, 0x75, 0x73, 0x65, 0x72, 0x73, 0x18, 0x01, 0x20, 0x03, 0x28, 0x0b,
	0x32, 0x0a, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x2e, 0x55, 0x73, 0x65, 0x72, 0x52, 0x05, 0x75, 0x73,
	0x65, 0x72, 0x73, 0x42, 0x0e, 0x5a, 0x0c, 0x2e, 0x2e, 0x2f, 0x75, 0x73, 0x65, 0x72, 0x3b, 0x55,
	0x73, 0x65, 0x72, 0x62, 0x06, 0x70, 0x72, 0x6f, 0x74, 0x6f, 0x33,
}

var (
	file_user_proto_rawDescOnce sync.Once
	file_user_proto_rawDescData = file_user_proto_rawDesc
)

func file_user_proto_rawDescGZIP() []byte {
	file_user_proto_rawDescOnce.Do(func() {
		file_user_proto_rawDescData = protoimpl.X.CompressGZIP(file_user_proto_rawDescData)
	})
	return file_user_proto_rawDescData
}

var file_user_proto_enumTypes = make([]protoimpl.EnumInfo, 2)
var file_user_proto_msgTypes = make([]protoimpl.MessageInfo, 3)
var file_user_proto_goTypes = []interface{}{
	(User_Gender)(0),  // 0: User.User.Gender
	(User_Type)(0),    // 1: User.User.Type
	(*User)(nil),      // 2: User.User
	(*UserList)(nil),  // 3: User.UserList
	(*User_Time)(nil), // 4: User.User.Time
	(*anypb.Any)(nil), // 5: google.protobuf.Any
}
var file_user_proto_depIdxs = []int32{
	4, // 0: User.User.time:type_name -> User.User.Time
	0, // 1: User.User.gender:type_name -> User.User.Gender
	1, // 2: User.User.type:type_name -> User.User.Type
	5, // 3: User.User.any:type_name -> google.protobuf.Any
	2, // 4: User.UserList.users:type_name -> User.User
	5, // [5:5] is the sub-list for method output_type
	5, // [5:5] is the sub-list for method input_type
	5, // [5:5] is the sub-list for extension type_name
	5, // [5:5] is the sub-list for extension extendee
	0, // [0:5] is the sub-list for field type_name
}

func init() { file_user_proto_init() }
func file_user_proto_init() {
	if File_user_proto != nil {
		return
	}
	if !protoimpl.UnsafeEnabled {
		file_user_proto_msgTypes[0].Exporter = func(v interface{}, i int) interface{} {
			switch v := v.(*User); i {
			case 0:
				return &v.state
			case 1:
				return &v.sizeCache
			case 2:
				return &v.unknownFields
			default:
				return nil
			}
		}
		file_user_proto_msgTypes[1].Exporter = func(v interface{}, i int) interface{} {
			switch v := v.(*UserList); i {
			case 0:
				return &v.state
			case 1:
				return &v.sizeCache
			case 2:
				return &v.unknownFields
			default:
				return nil
			}
		}
		file_user_proto_msgTypes[2].Exporter = func(v interface{}, i int) interface{} {
			switch v := v.(*User_Time); i {
			case 0:
				return &v.state
			case 1:
				return &v.sizeCache
			case 2:
				return &v.unknownFields
			default:
				return nil
			}
		}
	}
	file_user_proto_msgTypes[0].OneofWrappers = []interface{}{}
	type x struct{}
	out := protoimpl.TypeBuilder{
		File: protoimpl.DescBuilder{
			GoPackagePath: reflect.TypeOf(x{}).PkgPath(),
			RawDescriptor: file_user_proto_rawDesc,
			NumEnums:      2,
			NumMessages:   3,
			NumExtensions: 0,
			NumServices:   0,
		},
		GoTypes:           file_user_proto_goTypes,
		DependencyIndexes: file_user_proto_depIdxs,
		EnumInfos:         file_user_proto_enumTypes,
		MessageInfos:      file_user_proto_msgTypes,
	}.Build()
	File_user_proto = out.File
	file_user_proto_rawDesc = nil
	file_user_proto_goTypes = nil
	file_user_proto_depIdxs = nil
}

```
</details>


#### 使用生成的user.db.go 文件

grpc_study/grpc_message/main.go
```go
package main

import (
	"fmt"
	"github.com/golang/protobuf/proto"
	User "grpc_study/grpc_message/user"
)

func main() {
	address := "北京市天安门"

	userModel := User.User{
		Name:    "sreio",
		Age:     17,
		IsShow:  true,
		Height:  188.88,
		Hobbies: []string{"唱歌", "跳舞"},
		Address: &address,
	}

	marshal, err := proto.Marshal(&userModel)
	if err != nil {
		panic(err)
	}

	userModel2 := User.User{}
	err = proto.Unmarshal(marshal, &userModel2)
	if err != nil {
		panic(err)
	}

	fmt.Println(userModel2.String())
    // 获取单个字段
	fmt.Println(userModel2.GetName())
}

// 输出：
// name:"sreio" age:17 isShow:true height:188.88 hobbies:"唱歌" hobbies:"跳舞" address:"北京市天安门"
// sreio
```

