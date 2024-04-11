> go-elasticsearch Elasticsearch 的官方 Go 客户端

- GitHub仓库地址：https://github.com/elastic/go-elasticsearch


## 示例代码(基于v7)

```go
package main

import (
	"bytes"
	"context"
	"encoding/json"
	"fmt"
	es7 "github.com/elastic/go-elasticsearch/v7"
	"github.com/elastic/go-elasticsearch/v7/esapi"
	"log"
)

var Es *es7.Client

func init() {
	Es = content()
}

func main() {
	indexName := "go_sreio"
	//createIndex(indexName) // 创建索引
	//updateIndex(indexName) // 增加索引字段
	//getIndex(indexName) // 查询索引
	//delIndex(indexName) // 删除索引

	//doc := map[string]interface{}{
	//	"name": "sreio",
	//	"sex":  0,
	//	"other": map[string]interface{}{
	//		"hobby": "music,php,喝酒",
	//		"age":   17,
	//	},
	//}
	//createOrUpdateDoc(indexName, "2", doc) // 创建或更新文档
	//getDoc(indexName, "2")
	//delDoc(indexName, "2")

	search(indexName, map[string]interface{}{
		"query": map[string]interface{}{
			"match": map[string]interface{}{
				"name": "sreio",
			},
		},
	})
}

func content() *es7.Client {
	cfg := es7.Config{
		Addresses: []string{
			"http://127.0.0.1:9200", // Elasticsearch 地址
		},
	}

	es, err := es7.NewClient(cfg)
	if err != nil {
		log.Fatalf("Error creating the client: %s", err)
	}

	// 测试连接
	res, err := es.Info()
	if err != nil {
		log.Fatalf("Error getting response: %s", err)
	}
	defer res.Body.Close()

	var r map[string]interface{}
	if err := json.NewDecoder(res.Body).Decode(&r); err != nil {
		log.Fatalf("Error parsing the response body: %s", err)
	}

	fmt.Println("Elasticsearch version:", r["version"].(map[string]interface{})["number"])
	return es
}

func createIndex(indexName string) {
	// 创建索引
	//req := esapi.IndexRequest{
	//	Index:      indexName,                              // Index name
	//	Body:       strings.NewReader(`{"title" : "Test"}`), // Document body
	//	DocumentID: "1",                                     // Document ID
	//	Refresh:    "true",                                  // Refresh
	//}

	// 或者只创建索引
	req := esapi.IndicesCreateRequest{
		Index: indexName, // Index name
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error getting response: %s", err)
	}
	defer res.Body.Close()

	log.Println(res)

}

func updateIndex(indexName string) int {
	// 设置索引结构
	var indexStruct struct {
		Name  string `json:"name"`
		Sex   int    `json:"sex"`
		Other struct {
			Hobby string `json:"hobby"`
			Age   int    `json:"age"`
		} `json:"other"`
	}

	indexStructJson, _ := json.Marshal(indexStruct)

	// 创建索引
	req := esapi.IndexRequest{
		Index: indexName,                        // Index name
		Body:  bytes.NewReader(indexStructJson), // Document body
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error getting response: %s", err)
	}
	defer res.Body.Close()
	log.Println(res.String(), res.StatusCode)
	return res.StatusCode
}

func getIndex(indexName string) {
	req := esapi.IndicesGetRequest{
		Index: []string{indexName},
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error getting index: %s", err)
	}
	defer res.Body.Close()

	if res.IsError() {
		log.Fatalf("Error getting index: %s", res.Status())
	}

	log.Printf("Index '%s' information:\n%s", indexName, res.String())
}

func delIndex(indexName string) {
	req := esapi.IndicesDeleteRequest{
		Index: []string{indexName},
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error deleting index: %s", err)
	}
	defer res.Body.Close()

	if res.IsError() {
		log.Fatalf("Error deleting index: %s", res.Status())
	}

	log.Printf("Index '%s' deleted,status_code:%d", indexName, res.StatusCode)
}

func createOrUpdateDoc(indexName, docID string, doc map[string]interface{}) {
	docBytes, err := json.Marshal(doc)
	if err != nil {
		log.Fatalf("Error marshaling document: %s", err)
	}
	req := esapi.IndexRequest{
		Index:      indexName,
		DocumentID: docID,
		Body:       bytes.NewReader(docBytes),
	}
	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error creating document: %s", err)
	}

	defer res.Body.Close()

	if res.IsError() {
		log.Fatalf("Error creating document: %s", res.Status())
	}

	log.Printf("Document '%s' created,status_code:%d", docID, res.StatusCode)
}

func getDoc(indexName, docID string) {
	req := esapi.GetRequest{
		Index:      indexName,
		DocumentID: docID,
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error getting document: %s", err)
	}
	defer res.Body.Close()

	if res.IsError() {
		log.Fatalf("Error getting document: %s", res.Status())
	}

	var resMap map[string]interface{}
	if err := json.NewDecoder(res.Body).Decode(&resMap); err != nil {
		log.Fatalf("Error parsing document response: %s", err)
	}

	log.Printf("Document with ID '%s' in index '%s':\n%s", docID, indexName, resMap)
}

func delDoc(indexName, docID string) {
	req := esapi.DeleteRequest{
		Index:      indexName,
		DocumentID: docID,
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error deleting document: %s", err)
	}
	defer res.Body.Close()

	if res.IsError() {
		log.Fatalf("Error deleting document: %s", res.Status())
	}

	log.Printf("Document with ID '%s' deleted from index '%s'", docID, indexName)
}

func search(indexName string, query map[string]interface{}) map[string]interface{} {
	var buf bytes.Buffer
	if err := json.NewEncoder(&buf).Encode(query); err != nil {
		log.Fatalf("Error encoding query: %s", err)
	}

	req := esapi.SearchRequest{
		Index: []string{indexName},
		Body:  &buf,
		//RestTotalHitsAsInt: true,
	}

	res, err := req.Do(context.Background(), Es)
	if err != nil {
		log.Fatalf("Error searching: %s", err)
	}
	defer res.Body.Close()

	var resMap map[string]interface{}
	if err := json.NewDecoder(res.Body).Decode(&resMap); err != nil {
		log.Fatalf("Error parsing search response: %s", err)
	}
	log.Println(resMap)
	return resMap
}

```
