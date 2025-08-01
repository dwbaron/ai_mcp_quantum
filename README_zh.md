# QureGenAI & TyxonQ 平台 API 文档

[English](./README.md) | [中文](./README_zh.md)

## 简介

欢迎使用 QureGenAI 医图生科 和 TyxonQ 太玄量子 平台。我们的使命是通过一个强大、开发者友好的 HTTP API 和模型上下文协议 (MCP) 提供一整套全面的量子计算 + AI 药物发现（Quantum AIDD）服务。

我们的平台使研究人员、开发者和大型模型能够以编程方式访问最先进的计算模型，用于分子模拟、蛋白质结构预测等。

点击访问[QureGenAI](https://service.quregenai.com)

### 平台功能

#### ✅ 当前可用模型
- **✅ AutoDock GPU 分子对接** - 一种传统且快速的分子对接算法，具有可靠的精度，是高通量筛选的理想选择。[GitHub](https://github.com/ccsb-scripps/AutoDock-GPU)
- **✅ DiffDock 分子对接** - 一种基于扩散模型的 AI 对接模型，能够在没有预定义位置的情况下发现结合口袋并执行对接。[GitHub](https://github.com/gcorso/DiffDock)
- **✅ Protenix 蛋白质结构预测** - 仅从序列信息即可预测蛋白质结构和复合物模型。字节跳动开源的AlphaFold 3 PyTorch复现模型。[GitHub](https://github.com/bytedance/Protenix)

#### 🚧 即将推出的模型
- **🔄 Boltz-2** - 下一代生物分子结构预测模型，适用于蛋白质、核酸和小分子。[GitHub](https://github.com/jwohlwend/boltz)

- **🔄 ProteinMPNN** - 使用消息传递神经网络进行蛋白质序列设计的深度学习方法。[GitHub](https://github.com/dauparas/ProteinMPNN)

- **🔄 RoseTTAFold** - 使用基于注意力机制的神经网络进行准确的蛋白质结构预测。[GitHub](https://github.com/RosettaCommons/RoseTTAFold)

- **🔄 SurfDock** - 表面引导的分子对接，用于改进结合姿态预测。[GitHub](https://github.com/CAODH/SurfDock)

- **🔄 OpenFE** - 用于药物发现的开源自由能计算。[GitHub](https://github.com/OpenFreeEnergy/openfe)

- **🔄 DiffLinker** - 用于药物发现中分子连接器设计的等变扩散模型。[GitHub](https://github.com/igashov/DiffLinker)

- **🔄 MolMap** - 通过二维分子图谱进行分子表示学习。[GitHub](https://github.com/shenwanxiang/bidd-molmap)

- **🔄 AI2BMD** - AI驱动的生物分子动力学模拟和分析平台。[GitHub](https://github.com/microsoft/AI2BMD)

- **🔄 BioEmu AI** - 使用人工智能的高级生物系统仿真。[GitHub](https://github.com/microsoft/bioemu)



*我们正在不断扩展我们的模型集合，以提供全面的 AI 驱动的药物发现解决方案。敬请期待更多前沿模型！*

## 快速入门

### 基础 URL
所有 API 端点都相对于以下基础 URL：
```
https://api.quregenai.com
```

### 认证
所有 API 调用都需要使用 API 密钥进行认证。请将您的 API 密钥作为 Bearer 令牌包含在 `Authorization` 请求头中。

***获取令牌***: [获取密钥](https://service.quregenai.com/) 注册并获取您的 API 密钥

### 请求头
所有请求都应包含以下请求头：
```
Content-Type: application/json
Authorization: Bearer your_api_key_here
```

## API 参考

### 1. AutoDock GPU 分子对接用于高通量筛选

使用 AutoDock 算法执行分子对接。

- **端点**: `POST /api/autodock_api/autodock_docking`
- **描述**: 提交一个分子对接任务。您必须提供一个受体和三种配体输入类型中的一种。

#### 请求体

| 参数 | 类型 | 是否必需 | 描述 |
|---|---|---|---|
| `job_name` | string | 否 | 您的任务名称。 |
| `receptor` | string | 是 | 指向受体文件（例如 PDB 格式）的 URL 或服务器可访问路径。 |
| `center_x`, `center_y`, `center_z` | number/string | 是 | 对接盒子中心的坐标。 |
| `size_x`, `size_y`, `size_z` | number | 否 | 对接盒子的尺寸。每个轴默认为 15。 |
| `ligand` | string | 是 (三选一) | 单个配体文件（SDF, PDB, URL），文件大小不超过`10MB`|
| `ligands` | array of strings | 是 (三选一) | 多个配体文件的列表。 |
| `smiles` | string | 是 (三选一) | SMILES 字符串（多个分子使用 `\n` 分隔），或指向包含 SMILES 字符串（每行一个）的 `.txt` 文件。 |
| `thread` | integer | 否 | 线程数。默认为 1200，最小为 1000。 |

**注意**: 您必须提供 `ligand`、`ligands` 或 `smiles` 中的一种。

#### 示例：必须参数
- `receptor`: 受体本地文件/URL
  - 本地文件
  ```python
  # 本地文件路径
  path = '/path/to/file/receptor.pdb'
  with open(path, 'r') as f:
      content = f.read()
  receptor = {
    'content': content,
    'name': 'receptor.pdb'
  }
  ```
  - url
  ```python
  receptor = 'http://www.receptor.pdb'
  ```

- `center_x`, `center_y`, `center_z`: 对接盒子中心坐标（可以利用web应用的分子结构查看器选择）
- `size_x`, `size_y`, `size_z`: 对接盒子尺寸（默认都为15）
- 配体输入（三选一）：
  1. `ligand`: 单个配体，如果是本地文件则为键值（字典）对象，必须包含`content`字段，`name`字段可选，方便后续查看结果；如果是url可以直为字符串，（SDF格式文件，PDB格式文件，url链接地址）文件大小不超过`10MB`
  - 本地文件: 
    ```python     
    # 本地磁盘路径
    path = '/path/to/file/ligand.sdf'
    # 读取文件内容
    with open(path, 'r') as f:
        content = f.read()
    ligand = {
      'content': content,
      'name': 'ligand.sdf'
    }
    ```
  - url:
    ```python
    ligand = 'http://www.ligand.sdf'
    ```
  
  2. `ligands`: 多个配体 （多个文件，SDF格式，PDB格式， url链接地址）， 列表对象，其中每个文件大小不超过`10MB`
  - 本地文件
    ```python
    # 本地磁盘多个配体文件路径
    path1 = '/path/to/file/ligand1.sdf'
    path2 = '/path/to/file/ligand2.sdf'
    # 读取文件内容
    with open(path1, 'r') as f:
        content1 = f.read()
    with open(path2, 'r') as f:
        content2 = f.read()
    ligands = [
      {'name': 'ligand1.sdf',
       'content': content1 },
       {'name': 'ligand2.sdf',
       'content': content2}
    ]
    ```
  - url
    ```python
    ligands = ['http://www.ligand1.sdf', 
               'http://www.ligand2.sdf']
    ```

  3. `smiles`: SMILES模式
  - SMILES字符串，换行符`\n`分割多个分子
    ```python
    smiles = 'CCO\ncccccc'
    ```
  - 文件路径，本身是`txt`文件，包含多个分子smiles字符串，一个分子smiles一行）
    ```python
    # 本地文件
    path = '/path/to/file/smiles.txt'
    with open(path, 'r') as f:
        content = f.read()
    smiles = {
      'content': content
    }
    # url
    smiles = 'http://www.smiles.txt'
    
    ```



#### 示例1.1: 单个配体文件

**Python**
```python
import requests

# 读取本地配体文件内容，文件大小不超过10MB
with open('ligand.sdf', 'r') as f:
    ligand_content = f.read()

# 构造ligand文件对象
ligand = {
    'content': ligand_content,
    'name': 'ligand.sdf'
}

def submit_autodock_single_ligand():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "ligand": ligand,  # 使用文件对象格式
        # "ligand": "https://www.ligand.sdf"  # 或者使用URL链接
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"任务提交成功，任务ID: {result.get('task_id')}")
        return result
    else:
        print(f"请求失败: {response.status_code}, {response.text}")
        return None

# 调用示例
result = submit_autodock_single_ligand()
```

#### 示例1.2: 批量配体对接

**Python**
```python
    # 本地磁盘多个配体文件路径
    path1 = '/path/to/file/ligand1.sdf'
    path2 = '/path/to/file/ligand2.sdf'
    # 读取文件内容
    with open(path1, 'r') as f:
        content1 = f.read()
    with open(path2, 'r') as f:
        content2 = f.read()
    ligands = [
      {'name': 'ligand1.sdf',
       'content': content1 },
       {'name': 'ligand2.sdf',
       'content': content2}
    ]

def submit_autodock_multiple_ligands():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_Multiple_Ligands_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "ligands": ligands,
        # "ligands": ['https://www.ligand1.sdf', 'https://www.ligand2.sdf'],  # url链接列表
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"批量任务提交成功，任务ID: {result.get('task_id')}")
        return result
    else:
        print(f"请求失败: {response.status_code}, {response.text}")
        return None
```


#### 示例1.3: SMILES字符串对接
**Python**
```python

# 本地 smiles.txt 文本读取
path = '/path/to/file/smiles.txt'
with open(path, 'r') as f:
    content = f.read()
smiles = {
  'content': content
}

def submit_autodock_smiles():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    
    data = {
        "job_name": "AutoDock_SMILES_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": smiles,  # 本地smiles文件
        # "smiles": "CC(=O)CC(c1ccccc1)c1c(O)c2ccccc2oc1=O",  # 也可以是分子的smiles字符串
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        result = response.json()
        print(f"SMILES任务提交成功，任务ID: {result.get('task_id')}")
        return result
    else:
        print(f"请求失败: {response.status_code}, {response.text}")
        return None
```

---

### 2. DiffDock 分子对接

使用基于 AI 的 DiffDock 算法执行分子对接。

- **端点**: `POST /api/diffdock_api/diffdock_docking`
- **描述**: 提交一个 DiffDock 任务。当结合口袋未知时，此功能非常有用。

#### 请求体

| 参数 | 类型 | 是否必需 | 描述 |
|---|---|---|---|
| `job_name` | string | 否 | 您的任务名称。 |
| `protein` | string | 是 | 蛋白质文件路径（例如 PDB）或序列字符串。 |
| `ligand_file` | string | 是 (二选一) | 配体文件的路径。 |
| `smiles` | string | 是 (二选一) | 配体的 SMILES 字符串。 |
| `diffusion_steps` | integer | 否 | 扩散步数 (1-100)。默认值: 20。 |
| `sample_count` | integer | 否 | 生成的样本数 (1-100)。默认值: 10。 |

**注意**: 您必须提供 `ligand_file` 或 `smiles`。

#### 示例: 蛋白质序列和 SMILES

**Python**
```python
import requests
import os

BASE_URL = "https://api.quregenai.com"
API_KEY = os.environ.get("QUREGENAI_API_KEY", "<your_api_key_here>")

HEADERS = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

# 本地文件
with open('/path/to/file/receptor.pdb', 'r') as f:
    content = f.read()
protein = {
  'content': content,
  'name': 'protein.pdb'
}

# 也可以是 url
# protein = 'https://files.rcsb.org/download/7RT3.pdb'

def submit_diffdock_sequence_to_smiles():
    url = f"{BASE_URL}/api/diffdock_api/diffdock_docking"
    
    data = {
        "job_name": "DiffDock_Sequence_Test",
        "protein": protein,
        "smiles": "CCO",  # 乙醇
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("任务提交成功:", response.json())
        return response.json()
    else:
        print(f"错误: {response.status_code}", response.text)
        return None

# submit_diffdock_sequence_to_smiles()
```

---

### 3. Protenix 蛋白质结构预测

使用 Protenix 算法预测蛋白质结构和复合物模型。

- **端点**: `POST /api/protenix_api/protenix_predict`
- **描述**: 提交一个蛋白质结构预测任务，包括多链复合物以及与 DNA/RNA/配体的复合物。

#### 请求体

| 参数 | 类型 | 是否必需 | 描述 |
|---|---|---|---|
| `name` | string | 是 | 预测任务的名称。 |
| `sequences` | array of objects | 是 | 序列对象的数组。详见下文。 |
| `seeds` | integer | 否 | 随机种子。默认值: 42。 |
| `n_sample` | integer | 否 | 样本数。默认值: 5。 |
| `n_step` | integer | 否 | 步数。默认值: 150。 |
| `n_cycle` | integer | 否 | 循环数。默认值: 5。 |

`sequences` 对象具有以下结构：
| 键 | 类型 | 是否必需 | 描述 |
|---|---|---|---|
| `moduleType` | string | 是 | 序列类型: 'proteinChain', 'dnaSequence', 'rnaSequence', 'ligand'。 |
| `sequence` | string | 是 | 序列内容（蛋白质序列、DNA/RNA 序列或配体的 SMILES）。 |
| `copy` | integer | 是 | 此序列的副本数 (1-5)。 |


#### 示例: 蛋白质-配体复合物预测

**cURL**
```bash
curl -X POST "https://api.quregenai.com/api/protenix_api/protenix_predict" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your_api_key_here>" \
  -d '{
    "name": "Protein_Ligand_Complex",
    "sequences": [
      {
        "moduleType": "proteinChain",
        "sequence": "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN",
        "copy": 1
      },
      {
        "moduleType": "ligand",
        "sequence": "CCO",
        "copy": 1
      }
    ]
  }'
```

**Python**
```python
import requests
import os

BASE_URL = "https://api.quregenai.com"
API_KEY = os.environ.get("QUREGENAI_API_KEY", "<your_api_key_here>")

HEADERS = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

def submit_protenix_protein_ligand():
    url = f"{BASE_URL}/api/protenix_api/protenix_predict"
    protein_sequence = "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN"
    
    data = {
        "name": "Protein_Ligand_Complex",
        "sequences": [
            {
                "moduleType": "proteinChain",
                "sequence": protein_sequence,
                "copy": 1
            },
            {
                "moduleType": "ligand",
                "sequence": "CCO",  # 配体 SMILES
                "copy": 1
            }
        ]
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("任务提交成功:", response.json())
        return response.json()
    else:
        print(f"错误: {response.status_code}", response.text)
        return None

# submit_protenix_protein_ligand()
```

## 异步任务和 MCP 服务

由于这些模型的计算时间较长，所有 API 调用都是**异步**的。

1.  **提交任务**: 当您向 API 端点发送请求时，平台会验证您的请求并将任务排队等待执行。
2.  **接收任务 ID**: 如果提交成功，API 会返回一个 `200 OK` 响应，其中包含一个唯一的 `task_id`。

    ```json
    {
        "success": true,
        "message": "任务已提交到远程服务，正在异步处理中...",
        "task_id": "1-68775320-15633f89-dc4744aa5c31",
        "job_name": "AutoDock_FAA2F6144C4611F0A525DBA75ABC6F26_1752650526"
    }
    ```
3.  **监控任务状态**: 您可以使用 `task_id` 通过平台的 Web 界面监控任务的状态。登录 Web 门户，导航到相应的模型部分（例如 AutoDock），然后在历史记录列表中找到您的任务以查看其状态并检索结果。

**注意**: 用于以编程方式检查任务状态的专用 API 端点计划在未来版本中发布。

## 错误处理

API 使用标准的 HTTP 状态码来指示请求的成功或失败。

| 状态码 | 含义 | 示例错误消息 |
|---|---|---|
| `400 Bad Request` | 请求格式错误、缺少参数或参数冲突。 | `{"success": false, "message": "缺少必需参数: center_y"}` |
| `401 Unauthorized` | API 密钥缺失、无效或账户余额不足。 | `{"message":"缺少或无效的 Authorization 头部","success":false}` |
| `500 Internal Server Error` | 服务器上发生意外错误。 | 多种多样。 |


## 常见运行失败样例
### a. API 验证错误
```bash
# 传递了错误的API_KEY
{'message': '无效的API密钥', 'success': False}
```

### b. 余额不足
```bash
请求失败: 401, {"message":"QAU余额不足，当前余额: 0，至少需要5 QAU才能提交任务，请先充值","success":false}
```

### c. 缺少必要的参数
```python
    # 这里缺少了口袋中心坐标center_y 以及 center_z两个参数
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCO",
        "center_x": '-3.52',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
请求失败: 400, {
  "success": false,
  "message": "缺少必需参数: center_y（对接盒子参数）"
}
```

### d. 参数冗余
```python

    # 这里不但提供了smiles参数而且还有ligand参数，
    # 但只能提供其中的一项
    with open('/path/to/file/ligand.sdf', 'r') as f:
        content = f.read()
    
    ligand = {
      'content': content,
      'name': 'ligand.sdf'
    }
    
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCO",
        "ligand": ligand,
        "center_x": '-3.52',
        'center_y': '5.57',
        'center_z': '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
请求失败: 400, {
  "success": false,
  "message": "只能提供配体文件(ligand/ligands)或SMILES字符串中的一种"
}
```

### e. 配体文件错误上传成蛋白质pdb文件，而不是小分子
```python
    # 这里ligand参数给了一个蛋白质pdb本地文件而不是小分子

    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        'smiles': "CCO",
        "center_x": '-3.52',
        'center_y': '5.57',
        'center_z': '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```

```bash
请求失败: 400, {
  "success": false,
  "message": "检测到配体文件 '1uw6.pdb' 包含多个氨基酸残基 (TYR, MET, THR, HIS, ARG...)，这似乎是一个蛋白质文件。本平台只支持蛋白质与小分子对接，不支持蛋白质与蛋白质对接。请检查您的配体文件内容。"
}
```

### f. 配体结构错误导致无法转换PDBQT成功
```python
    # 这里给了一个结构错误的smiles
    # 需要严格检查配体和受体蛋白的结构正确性
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "smiles": "CCCCCcccccccccc",
        "center_x": '-3.52',
        "center_y": '5.57',
        "center_z": '-26.55',
        "size_x": 15,
        "size_y": 15,
        "size_z": 15,
        "thread": 1200
    }
```



## 最佳实践

- **序列长度**:
  - 蛋白质: 10-1000 个氨基酸
  - DNA/RNA: 10-1000 个碱基
- **文件格式**:
  - 蛋白质: PDB
  - 配体: SDF, MOL2, MOL
  - SMILES: 标准 SMILES 字符串
- **性能**:
  - 为 AutoDock 使用适当的线程数（建议 1000-2000）。
  - 对于大型输入文件，使用 URL 以避免过大的请求体。
- **任务管理**:
  - 存储每个已提交任务的 `task_id`。这对于跟踪和检索结果至关重要。

## 支持

如果您遇到任何问题或有疑问，请联系我们的技术支持团队 code@quregenai.com 或查阅 Web 界面中提供的详细错误日志。

#### 微信公众号 | Official WeChat
<img src="docs/images/wechat_offical_qrcode.jpg" alt="TyxonQ 微信公众号" width="200">

#### 开发者交流群 | Developer Community
<img src="docs/images/developer_group_qrcode.png" alt="TyxonQ 开发者交流群" width="200">

*扫码关注公众号获取最新资讯 | Scan to follow for latest updates*  
*扫码加入开发者群进行技术交流 | Scan to join developer community*