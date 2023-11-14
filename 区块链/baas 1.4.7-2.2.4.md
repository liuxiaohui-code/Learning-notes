[TOC]

# 更新记录

##  1. go.mod
```go
  github.com/hyperledger/fabric-sdk-go: v1.0.0-alpha5 -> v1.0.0
  github.com/hyperledger/fabric: v1.4.7 -> v2.1.1+incompatible -> 本地release2-2
  go: 1.13 -> 1.14
```
##  2. third_party
###  2.1. fabric
``` 
  # internal 包私有，不可调用
  # 替代方法： 
  #  1. 下载fabric包到本地third_party,copy internal -> internal2  
  #  2. 弃用internal，使用fabric-config包替换  

  # 以下使用方法1

  fabric/bccsp/utils: 新增 extra.go ,附录附有代码
```


###  2.2. fabric-sdk-go

```
third_party/fabric-sdk-go/internal/github.com/hyperledger/fabric/bccsp/opts.go: 新增常量 GMSM2 = "GMSM2"  // 附录有代码实现

fabric-sdk-go/pkg/core/cryptosuite/cryptosuite.go: 新增两个函数  //附录附有代码
```


##  3. 附录
###  3.1. fabric/bccsp/utils/extra.go
```go
//extra.go
package utils

import (
	"crypto/ecdsa"
	"crypto/rand"
	"crypto/rsa"
	"crypto/x509"
	"encoding/pem"
	"errors"
	"fmt"
	"io"
	"os"
)

// Clone clones the passed slice
func Clone(src []byte) []byte {
	clone := make([]byte, len(src))
	copy(clone, src)

	return clone
}
// PEMtoPrivateKey unmarshals a pem to private key
func PEMtoPrivateKey(raw []byte, pwd []byte) (interface{}, error) {
	if len(raw) == 0 {
		return nil, errors.New("Invalid PEM. It must be different from nil.")
	}
	block, _ := pem.Decode(raw)
	if block == nil {
		return nil, fmt.Errorf("Failed decoding PEM. Block must be different from nil. [% x]", raw)
	}

	// TODO: derive from header the type of the key

	if x509.IsEncryptedPEMBlock(block) {
		if len(pwd) == 0 {
			return nil, errors.New("Encrypted Key. Need a password")
		}

		decrypted, err := x509.DecryptPEMBlock(block, pwd)
		if err != nil {
			return nil, fmt.Errorf("Failed PEM decryption [%s]", err)
		}

		key, err := DERToPrivateKey(decrypted)
		if err != nil {
			return nil, err
		}
		return key, err
	}

	cert, err := DERToPrivateKey(block.Bytes)
	if err != nil {
		return nil, err
	}
	return cert, err
}
// PublicKeyToPEM marshals a public key to the pem format
func PublicKeyToPEM(publicKey interface{}, pwd []byte) ([]byte, error) {
	if len(pwd) != 0 {
		return PublicKeyToEncryptedPEM(publicKey, pwd)
	}

	if publicKey == nil {
		return nil, errors.New("Invalid public key. It must be different from nil.")
	}

	switch k := publicKey.(type) {
	case *ecdsa.PublicKey:
		if k == nil {
			return nil, errors.New("Invalid ecdsa public key. It must be different from nil.")
		}
		PubASN1, err := x509.MarshalPKIXPublicKey(k)
		if err != nil {
			return nil, err
		}

		return pem.EncodeToMemory(
			&pem.Block{
				Type:  "PUBLIC KEY",
				Bytes: PubASN1,
			},
		), nil
	case *rsa.PublicKey:
		if k == nil {
			return nil, errors.New("Invalid rsa public key. It must be different from nil.")
		}
		PubASN1, err := x509.MarshalPKIXPublicKey(k)
		if err != nil {
			return nil, err
		}

		return pem.EncodeToMemory(
			&pem.Block{
				Type:  "RSA PUBLIC KEY",
				Bytes: PubASN1,
			},
		), nil

	default:
		return nil, errors.New("Invalid key type. It must be *ecdsa.PublicKey or *rsa.PublicKey")
	}
}
// DirMissingOrEmpty checks is a directory is missing or empty
func DirMissingOrEmpty(path string) (bool, error) {
	dirExists, err := DirExists(path)
	if err != nil {
		return false, err
	}
	if !dirExists {
		return true, nil
	}

	dirEmpty, err := DirEmpty(path)
	if err != nil {
		return false, err
	}
	if dirEmpty {
		return true, nil
	}
	return false, nil
}

// DirExists checks if a directory exists
func DirExists(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, nil
	}
	return false, err
}

// DERToPrivateKey unmarshals a der to private key
func DERToPrivateKey(der []byte) (key interface{}, err error) {

	if key, err = x509.ParsePKCS1PrivateKey(der); err == nil {
		return key, nil
	}

	if key, err = x509.ParsePKCS8PrivateKey(der); err == nil {
		switch key.(type) {
		case *rsa.PrivateKey, *ecdsa.PrivateKey:
			return
		default:
			return nil, errors.New("Found unknown private key type in PKCS#8 wrapping")
		}
	}

	if key, err = x509.ParseECPrivateKey(der); err == nil {
		return
	}

	return nil, errors.New("Invalid key type. The DER must contain an rsa.PrivateKey or ecdsa.PrivateKey")
}

// PublicKeyToEncryptedPEM converts a public key to encrypted pem
func PublicKeyToEncryptedPEM(publicKey interface{}, pwd []byte) ([]byte, error) {
	if publicKey == nil {
		return nil, errors.New("Invalid public key. It must be different from nil.")
	}
	if len(pwd) == 0 {
		return nil, errors.New("Invalid password. It must be different from nil.")
	}

	switch k := publicKey.(type) {
	case *ecdsa.PublicKey:
		if k == nil {
			return nil, errors.New("Invalid ecdsa public key. It must be different from nil.")
		}
		raw, err := x509.MarshalPKIXPublicKey(k)
		if err != nil {
			return nil, err
		}

		block, err := x509.EncryptPEMBlock(
			rand.Reader,
			"PUBLIC KEY",
			raw,
			pwd,
			x509.PEMCipherAES256)

		if err != nil {
			return nil, err
		}

		return pem.EncodeToMemory(block), nil

	default:
		return nil, errors.New("Invalid key type. It must be *ecdsa.PublicKey")
	}
}
// DirEmpty checks if a directory is empty
func DirEmpty(path string) (bool, error) {
	f, err := os.Open(path)
	if err != nil {
		return false, err
	}
	defer f.Close()

	_, err = f.Readdir(1)
	if err == io.EOF {
		return true, nil
	}
	return false, err
}
// ErrToString converts and error to a string. If the error is nil, it returns the string "<clean>"
func ErrToString(err error) string {
	if err != nil {
		return err.Error()
	}

	return "<clean>"
}

```

###  3.2. fabric-sdk-go/pkg/core/cryptosuite/cryptosuite.go
```go
// GetECDSAPrivateKeyImportOpts returns options for ECDSA key import.
func GetECDSAPrivateKeyImportOpts(ephemeral bool) core.KeyImportOpts {
	return &bccsp.ECDSAPrivateKeyImportOpts{Temporary: ephemeral}
}

// GetGMSM2PrivateKeyImportOpts returns options for ECDSA key import.
func GetGMSM2PrivateKeyImportOpts(ephemeral bool) core.KeyImportOpts {
	return &bccsp.GMSM2PrivateKeyImportOpts{Temporary: ephemeral}
}
```
###  3.3. third_party/fabric-sdk-go/internal/github.com/hyperledger/fabric/bccsp/opts.go
```go
// 新增
	// GMSM2
const	GMSM2 = "GMSM2"

// GMSM2PrivateKeyImportOpts  实现  bccsp.KeyImportOpts 接口
type GMSM2PrivateKeyImportOpts struct {
	Temporary bool
}

// Algorithm returns the key importation algorithm identifier (to be used).
func (opts *GMSM2PrivateKeyImportOpts) Algorithm() string {
	return GMSM2
}

// Ephemeral returns true if the key generated has to be ephemeral,
// false otherwise.
func (opts *GMSM2PrivateKeyImportOpts) Ephemeral() bool {
	return opts.Temporary
}

```

##  4. import
```
localconfig
  github.com/hyperledger/fabric/internal/configtxgen/localconfig: -> github.com/hyperledger/fabric/internal/configtxgen/genesisconfig

protolator
  github.com/hyperledger/fabric/internal/protolator: -> github.com/hyperledger/fabric-config/protolator

internal
  github.com/hyperledger/fabric/internal: -> github.com/hyperledger/fabric/internal2

cauthdsl
  github.com/hyperledger/fabric/common/cauthdsl: -> github.com/hyperledger/fabric/common/policydsl

utils
  github.com/hyperledger/fabric-protos-go/utils: -> github.com/hyperledger/fabric/protoutil

tools: 包含configtxlator等包
  github.com/hyperledger/fabric/common/tools: -> github.com/hyperledger/fabric/internal2

shim 
    github.com/hyperledger/fabric/core/chaincode/shim: -> github.com/hyperledger/fabric-chaincode-go/shim

peer
    github.com/hyperledger/fabric/protos/peer: -> github.com/hyperledger/fabric-protos-go/peer

protos 
  github.com/hyperledger/fabric-sdk-go/third_party/: ->
  github.com/hyperledger/fabric/protos: -> github.com/hyperledger/fabric-protos-go

```

##  5. 部分函数在新版本被废弃
```
pkg/configgen/configgen.go 
	import (cb "github.com/hyperledger/fabric/protos/common")
	废弃函数： cb.NewConfigGroup()
	替换： &cb.ConfigGroup{ Groups:make(map[string]*cb.ConfigGroup),Values:make(map[string]*cb.ConfigValue),Policies:make(map[string]*cb.ConfigPolicy),}

internal/service/v2/networkv2.go
	废弃函数：sm3hash.Write(block.Data.Bytes()) 
	替换： sm3hash.Write(futil.ConcatenateBytes(block.Data.Data...))
```
##  6. NodeOU
```
internal/service/msp/mspv2.go:
  	EnableOU = true

internal/service/v2/channelv2.go:
internal/service/v2/networkv2.go:
	&common.Organization{
		...,
		NodeOU = true,  // 新增
	}

```
##  7. 接口实现变动
```go
//   pkg/fabricsdk/implement/endpointconfig.go

//修改前
func (e *EndpointConfig) OrdererConfig(nameOrURL string) (*fab.OrdererConfig, bool) {
	orderer, err := e.callback.GetOrderer(nameOrURL)
	if err != nil {
		return nil, false
	}

	o := newInternalOrderer(orderer)
	return &o, true
}

// 修改后
func (e *EndpointConfig) OrdererConfig(nameOrURL string) (*fab.OrdererConfig, bool, bool) {
	orderer, err := e.callback.GetOrderer(nameOrURL)
	if err != nil {
		return nil, false, false
	}

	o := newInternalOrderer(orderer)
	return &o, true, false
}

// 
var(
	//default grpc opts
	defaultKeepAliveTime    = 0
	defaultKeepAliveTimeout = time.Second * 20
	defaultKeepAlivePermit  = false
	defaultFailFast         = false
	defaultAllowInsecure    = false
)
func newInternalOrderer(o OrdererConfig) fab.OrdererConfig {
	return fab.OrdererConfig{
		URL:       o.URL,
		TLSCACert: parseX509Certificate(o.TLSCACertPem),
		GRPCOptions: map[string]interface{}{   // 新增
			"ssl-target-name-override": o.Name,
			"keep-alive-time":          defaultKeepAliveTime,
			"keep-alive-timeout":       defaultKeepAliveTimeout,
			"keep-alive-permit":        defaultKeepAlivePermit,
			"fail-fast":                defaultFailFast,
			"allow-insecure":           defaultAllowInsecure,
		},
	}
}

func newInternalPeer(p PeerConfig) fab.PeerConfig {
	return fab.PeerConfig{URL: p.URL, TLSCACert: parseX509Certificate(p.TLSCACertPem),
		GRPCOptions: map[string]interface{}{ // 新增
			"ssl-target-name-override": p.Name,
			"keep-alive-time":          defaultKeepAliveTime,
			"keep-alive-timeout":       defaultKeepAliveTimeout,
			"keep-alive-permit":        defaultKeepAlivePermit,
			"fail-fast":                defaultFailFast,
			"allow-insecure":           defaultAllowInsecure,
		},
	}
}
```
##  8. 新功能实现（Lifecycle 链码操作）

接口功能：  lifecycle 打包、安装、审批、提交、查询链码
接口定义： 	pkg/fabricsdk/service.go
数据模型：	pkg/fabricsdk/model.go
接口实现：	pkg/fabricsdk/implement/lifecycle.go

service层调用： internal/service/v2/contractv2lifecycle.go

api层调用：interna;/api/v2/contract_lifecycle.go

##  9. default.go 
```go
func SetDefault(version string) {
//....
	case "2.2":
		DefaultCapabilitiesOrderer["V2_0"] = true
		DefaultCapabilitiesChannel["V2_0"] = true
		DefaultCapabilitiesApplication["V2_0"] = true
		DefaultApplication.ACLs = DefaultACLs
//....
}
var DefaultACLs = map[string]string{
//...新增
	"_lifecycle/CheckCommitReadiness": "/Channel/Application/Writers",
	"_lifecycle/CommitChaincodeDefinition": "/Channel/Application/Writers",
	"_lifecycle/QueryChaincodeDefinition": "/Channel/Application/Readers",
	"_lifecycle/QueryChaincodeDefinitions": "/Channel/Application/Readers",
//...
}
var DefaultPoliciesApplication = map[string]*tc.Policy{
// ... 新增
	"LifecycleEndorsement": {Type: "ImplicitMeta", Rule: "MAJORITY Endorsement"},
	"Endorsement":          {Type: "ImplicitMeta", Rule: "MAJORITY Endorsement"},
}
var DefaultPoliciesOrganization = map[string]*tc.Policy{
// ... 应用组织新增，
	"Endorsement": {Type: "Signature", Rule: "OR('SampleOrg.member')"},
/*
在pkg/configgen/blockprovider/common.go中，以下函数增加一个参数isPeer以对不同组织匹配相应的Policy
func CreateOrgCfgByOrg(org common.Organization, isPeer bool) (*genesisconfig.Organization, error) {...}
*/
}
```