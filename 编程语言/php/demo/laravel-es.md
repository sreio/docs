## composer安装
> composer require elasticsearch/elasticsearch
>
> 注：支持 es 版本为 7.1，使用 composer 下载 es 应标注 7.1 的版本，否则会默认下载最新版的 es，而最新版的 es 引不到 Elasticsearch\ClientBuilde，这篇代码也不会走通

## ES封装
```php
<?php
namespace App\Es;

use Elasticsearch\ClientBuilder;

class MyEs
{
    //ES客户端链接
    private $client;

    /**
     * 构造函数
     * MyElasticsearch constructor.
     */
    public function __construct()
    {
        $this->client = ClientBuilder::create()->setHosts(['127.0.0.1:9200'])->build();
    }

    /**
     * 判断索引是否存在
     * @param string $index_name
     * @return bool|mixed|string
     */
    public function exists_index($index_name = 'test_ik')
    {
        $params = [
            'index' => $index_name
        ];
        try {
            return $this->client->indices()->exists($params);
        } catch (\Elasticsearch\Common\Exceptions\BadRequest400Exception $e) {
            $msg = $e->getMessage();
            $msg = json_decode($msg,true);
            return $msg;
        }
    }

    /**
     * 创建索引
     * @param string $index_name
     * @return array|mixed|string
     */
    public function create_index($index_name = 'test_ik') { // 只能创建一次
        $params = [
            'index' => $index_name,
            'body' => [
                'settings' => [
                    'number_of_shards' => 5,
                    'number_of_replicas' => 1
                ]
            ]
        ];
        try {
            return $this->client->indices()->create($params);
        } catch (\Elasticsearch\Common\Exceptions\BadRequest400Exception $e) {
            $msg = $e->getMessage();
            $msg = json_decode($msg,true);
            return $msg;
        }
    }

    /**
     * 删除索引
     * @param string $index_name
     * @return array
     */
    public function delete_index($index_name = 'test_ik') {
        $params = ['index' => $index_name];
        $response = $this->client->indices()->delete($params);
        return $response;
    }

    /**
     * 添加文档
     * @param $id
     * @param $doc ['id'=>100, 'title'=>'phone']
     * @param string $index_name
     * @param string $type_name
     * @return array
     */
    public function add_doc($id,$doc,$index_name = 'test_ik',$type_name = 'goods') {
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'id' => $id,
            'body' => $doc
        ];
        $response = $this->client->index($params);
        return $response;
    }

    /**
     * 判断文档存在
     * @param int $id
     * @param string $index_name
     * @param string $type_name
     * @return array|bool
     */
    public function exists_doc($id = 1,$index_name = 'test_ik',$type_name = 'goods') {
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'id' => $id
        ];
        $response = $this->client->exists($params);
        return $response;
    }

    /**
     * 获取文档
     * @param int $id
     * @param string $index_name
     * @param string $type_name
     * @return array
     */
    public function get_doc($id = 1,$index_name = 'test_ik',$type_name = 'goods') {
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'id' => $id
        ];
        $response = $this->client->get($params);
        return $response;
    }

    /**
     * 更新文档
     * @param int $id
     * @param string $index_name
     * @param string $type_name
     * @param array $body ['doc' => ['title' => '苹果手机iPhoneX']]
     * @return array
     */
    public function update_doc($id = 1,$index_name = 'test_ik',$type_name = 'goods', $body=[]) {
        // 可以灵活添加新字段,最好不要乱添加
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'id' => $id,
            'body' => $body
        ];
        $response = $this->client->update($params);
        return $response;
    }

    /**
     * 删除文档
     * @param int $id
     * @param string $index_name
     * @param string $type_name
     * @return array
     */
    public function delete_doc($id = 1,$index_name = 'test_ik',$type_name = 'goods') {
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'id' => $id
        ];
        $response = $this->client->delete($params);
        return $response;
    }

    /**
     * 搜索文档 (分页，排序，权重，过滤)
     * @param string $index_name
     * @param string $type_name
     * @param array $body
     * $body = [
                'query' => [
                    'match' => [
                        'fang_name' => [
                            'query' => $fangName
                        ]
                    ]
                ],
                'highlight'=>[
                    'fields'=>[
                        'fang_name'=>[
                            'pre_tags'=>[
                                '<span style="color: red">'
                            ],
                            'post_tags'=>[
                                '</span>'
                            ]
                        ]
                    ]
                ]
            ];
     * @return array
     */
    public function search_doc($index_name = "test_ik",$type_name = "goods",$body=[]) {
        $params = [
            'index' => $index_name,
            'type' => $type_name,
            'body' => $body
        ];
        $results = $this->client->search($params);
        return $results;
    }

}

```
## 将数据表中所有数据添加至ES
```php
public function esAdd()
    {
        $data = Good::get()->toArray();
        $es = new MyEs();
        if (!$es->exists_index('goods')) {
			//创建es索引，es的索引相当于MySQL的数据库
            $es->create_index('goods');
        }
        foreach ($data as $model) {
            $es->add_doc($model['id'], $model, 'goods', '_doc');
        }

    }

```

## 每在MySQL里添加一条数据，在es里也添加一条
> 直接将代码补在MySQL添加入库的逻辑方法里即可

```php
		//添加至MySQL
		$res=Good::insertGetId($arr);

        $es = new MyEs();
        if (!$es->exists_index('goods')) {
            $es->create_index('goods');
        }
		//添加至es
        $es->add_doc($res, $arr, 'goods', '_doc');

        return $res;
```
## 进行MySQL数据修改时，也更新es的数据
> 直接将代码补在MySQL修改数据的逻辑方法里即可

```php
		//修改MySQL的数据
        $res=Good::where('id',$id)->update($arr);

        $es = new MyEs();
        if (!$es->exists_index('goods')) {
            $es->create_index('goods');
        }
		//修改es的数据
        $es->update_doc($id, 'goods', '_doc',['doc'=>$arr]);

        return $res;
```

## 通过ES实现搜索功能
```php
public function search()
    {
		//获取搜索值
        $search = \request()->get('search');
        if (!empty($search)) {
            $es = new MyEs();
            $body = [
                'query' => [
                    'match' => [
                        'title' => [
                            'query' => $search
                        ]
                    ]
                ],
                'highlight'=>[
                    'fields'=>[
                        'title'=>[
                            'pre_tags'=>[
                                '<span style="color: red">'
                            ],
                            'post_tags'=>[
                                '</span>'
                            ]
                        ]
                    ]
                ]
            ];
            $res = $es->search_doc('goods', '_doc', $body);
            $data = array_column($res['hits']['hits'], '_source');
            foreach ($data as $key=>&$v){
                 $v['title'] = $res['hits']['hits'][$key]['highlight']['title'][0];
            }
            unset($v);
            return $data;
        }
        $data = Good::get();
        return $data;
    }

```