# QureGenAI & TyxonQ Platform API Documentation

[English](./README.md) | [中文](./README_zh.md)

## Introduction

Welcome to the QureGenAI 医图生科 and TyxonQ 太玄量子 platforms. Our mission is to provide a comprehensive suite of Quantum Computing + AI for Drug Discovery (Quantum AIDD) services through a powerful, developer-friendly HTTP API and Model Context Protocol (MCP).

Our platform enables researchers, developers, and large models to programmatically access state-of-the-art computational models for molecular simulation, protein structure prediction, and more.

Click To Use [QureGenAI](https://service.quregenai.com)




### Platform Features

#### ✅ Currently Available Models
- **✅ AutoDock GPU Molecular Docking** - A traditional and fast molecular docking algorithm with reliable accuracy, ideal for high-throughput screening. [GitHub](https://github.com/ccsb-scripps/AutoDock-GPU)
- **✅ DiffDock Molecular Docking** - An AI-powered docking model based on diffusion, capable of discovering binding pockets and performing docking without a predefined location. [GitHub](https://github.com/gcorso/DiffDock)
- **✅ Protenix Protein Structure Prediction** - Predicts protein structures and complex models from sequence information alone. A trainable, open-source PyTorch reproduction of AlphaFold 3. [GitHub](https://github.com/bytedance/Protenix)


#### 🚧 Coming Soon Models

- **🔄 Boltz-2** - Next-generation biomolecular structure prediction model for proteins, nucleic acids, and small molecules. [GitHub](https://github.com/jwohlwend/boltz)

- **🔄 ProteinMPNN** - Deep learning method for protein sequence design using message passing neural networks. [GitHub](https://github.com/dauparas/ProteinMPNN)

- **🔄 RoseTTAFold** - Accurate protein structure prediction using attention-based neural networks. [GitHub](https://github.com/RosettaCommons/RoseTTAFold)

- **🔄 SurfDock** - Surface-guided molecular docking for improved binding pose prediction. [GitHub](https://github.com/CAODH/SurfDock)

- **🔄 OpenFE** - Open-source alchemical free energy calculations for drug discovery. [GitHub](https://github.com/OpenFreeEnergy/openfe)

- **🔄 DiffLinker** - Equivariant diffusion model for molecular linker design in drug discovery. [GitHub](https://github.com/igashov/DiffLinker)

- **🔄 MolMap** - Molecular representation learning through 2D molecular maps. [GitHub](https://github.com/shenwanxiang/bidd-molmap)

- **🔄 AI2BMD** - AI-powered biomolecular dynamics simulation and analysis platform. [GitHub](https://github.com/microsoft/AI2BMD)

- **🔄 BioEmu AI** - Advanced biological system emulation using 
artificial intelligence. [GitHub](https://github.com/microsoft/bioemu)


*We are continuously expanding our model collection to provide comprehensive AI-powered drug discovery solutions. Stay tuned for more cutting-edge models!*


## Getting Started

### Base URL
All API endpoints are relative to the following base URL:
```
https://api.quregenai.com
```

### Authentication
All API calls require authentication using an API Key. Include your API key in the `Authorization` header as a Bearer token.

***Token Access***: [Getting a Key](https://service.quregenai.com/) to register and obtain your API key


### Headers
All requests should include the following headers:
```
Content-Type: application/json
Authorization: Bearer your_api_key_here
```

## API Reference

### 1. AutoDock GPU Molecular Docking for High Throughput Screen

Performs molecular docking using the AutoDock algorithm.

- **Endpoint**: `POST /api/autodock_api/autodock_docking`
- **Description**: Submits a job for molecular docking. You must provide a receptor and one of the three ligand input types.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_name` | string | No | A name for your job. |
| `receptor` | string | Yes | URL or server-accessible path to the receptor file (e.g., PDB format). |
| `center_x`, `center_y`, `center_z` | number/string | Yes | Coordinates for the center of the docking box. |
| `size_x`, `size_y`, `size_z` | number | No | Dimensions of the docking box. Defaults to 15 for each axis. |
| `ligand` | string | Yes (one of three) | Path to a single ligand file (SDF, PDB). |
| `ligands` | array of strings | Yes (one of three) | A list of paths to multiple ligand files. |
| `smiles` | string | Yes (one of three) | A SMILES string (use `\n` for multiple), or a path to a `.txt` file with SMILES strings (one per line). |
| `thread` | integer | No | Number of threads. Default is 1200, min is 1000. |

**Note**: You must provide exactly one of `ligand`, `ligands`, or `smiles`.

#### Example: Single Ligand File

**cURL**
```bash
curl -X POST "https://api.quregenai.com/api/autodock_api/autodock_docking" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your_api_key_here>" \
  -d '{
    "job_name": "AutoDock_Single_Ligand_Test",
    "receptor": "https://files.rcsb.org/download/7RT3.pdb",
    "ligand": "/path/to/your/ligand.sdf",
    "center_x": -3.52,
    "center_y": 5.57,
    "center_z": -26.55,
    "size_x": 15,
    "size_y": 15,
    "size_z": 15
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

def submit_autodock_single_ligand():
    url = f"{BASE_URL}/api/autodock_api/autodock_docking"
    data = {
        "job_name": "AutoDock_Single_Ligand_Test",
        "receptor": "https://files.rcsb.org/download/7RT3.pdb",
        "ligand": "/path/to/your/ligand.sdf",
        "center_x": -3.52,
        "center_y": 5.57,
        "center_z": -26.55
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("Job submitted successfully:", response.json())
        return response.json()
    else:
        print(f"Error: {response.status_code}", response.text)
        return None

# submit_autodock_single_ligand()
```

---

### 2. DiffDock Molecular Docking

Performs molecular docking using the AI-based DiffDock algorithm.

- **Endpoint**: `POST /api/diffdock_api/diffdock_docking`
- **Description**: Submits a DiffDock job. This is useful when the binding pocket is unknown.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_name` | string | No | A name for your job. |
| `protein` | string | Yes | Protein file path (e.g. PDB) or sequence string. |
| `ligand_file` | string | Yes (one of two) | Path to a ligand file. |
| `smiles` | string | Yes (one of two) | A SMILES string for the ligand. |
| `diffusion_steps` | integer | No | Number of diffusion steps (1-100). Default: 20. |
| `sample_count` | integer | No | Number of samples to generate (1-100). Default: 10. |

**Note**: You must provide either `ligand_file` or `smiles`.

#### Example: Protein Sequence and SMILES

**cURL**
```bash
curl -X POST "https://api.quregenai.com/api/diffdock_api/diffdock_docking" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <your_api_key_here>" \
  -d '{
    "job_name": "DiffDock_Sequence_Test",
    "protein": "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN",
    "smiles": "CCO"
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

def submit_diffdock_sequence_to_smiles():
    url = f"{BASE_URL}/api/diffdock_api/diffdock_docking"
    protein_sequence = "MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQAPILSRVGDGTQDNLSGAEKAVQVKVKALPDAQFEVVHSLAKWKREQTGQGWVPSNYITPVN"
    
    data = {
        "job_name": "DiffDock_Sequence_Test",
        "protein": protein_sequence,
        "smiles": "CCO",  # Ethanol
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("Job submitted successfully:", response.json())
        return response.json()
    else:
        print(f"Error: {response.status_code}", response.text)
        return None

# submit_diffdock_sequence_to_smiles()
```

---

### 3. Protenix Protein Structure Prediction

Predicts protein structure and complex models using the Protenix algorithm.

- **Endpoint**: `POST /api/protenix_api/protenix_predict`
- **Description**: Submits a job for protein structure prediction, including multi-chain complexes and complexes with DNA/RNA/ligands.

#### Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | A name for the prediction job. |
| `sequences` | array of objects | Yes | An array of sequence objects. See details below. |
| `seeds` | integer | No | Random seed. Default: 42. |
| `n_sample` | integer | No | Number of samples. Default: 5. |
| `n_step` | integer | No | Number of steps. Default: 150. |
| `n_cycle` | integer | No | Number of cycles. Default: 5. |

The `sequences` object has the following structure:
| Key | Type | Required | Description |
|---|---|---|---|
| `moduleType` | string | Yes | Type of sequence: 'proteinChain', 'dnaSequence', 'rnaSequence', 'ligand'. |
| `sequence` | string | Yes | The sequence content (protein sequence, DNA/RNA sequence, or SMILES for ligand). |
| `copy` | integer | Yes | Number of copies of this sequence (1-5). |


#### Example: Protein-Ligand Complex Prediction

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
                "sequence": "CCO",  # Ligand SMILES
                "copy": 1
            }
        ]
    }
    
    response = requests.post(url, headers=HEADERS, json=data)
    
    if response.status_code == 200:
        print("Job submitted successfully:", response.json())
        return response.json()
    else:
        print(f"Error: {response.status_code}", response.text)
        return None

# submit_protenix_protein_ligand()
```

## Asynchronous Tasks and MCP Service

Due to the long computation times for these models, all API calls are **asynchronous**.

1.  **Submit a Job**: When you send a request to an API endpoint, the platform validates your request and queues a job for execution.
2.  **Receive a Task ID**: If the submission is successful, the API returns a `200 OK` response containing a unique `task_id`.

    ```json
    {
        "success": true,
        "message": "Task submitted to remote service, processing asynchronously...",
        "task_id": "1-68775320-15633f89-dc4744aa5c31",
        "job_name": "AutoDock_FAA2F6144C4611F0A525DBA75ABC6F26_1752650526"
    }
    ```
3.  **Monitor Task Status**: You can monitor the status of your job using the `task_id` through the platform's web interface. Log in to the web portal, navigate to the appropriate model section (e.g., AutoDock), and find your task in the history list to view its status and retrieve results.

**Note**: A dedicated API endpoint for programmatically checking task status is planned for a future release.

## Error Handling

The API uses standard HTTP status codes to indicate the success or failure of a request.

| Status Code | Meaning | Example Error Message |
|---|---|---|
| `400 Bad Request` | The request was malformed, missing parameters, or had conflicting parameters. | `{"success": false, "message": "Missing required parameter: center_y"}` |
| `401 Unauthorized` | The API key is missing, invalid, or the account has insufficient funds. | `{"message":"Invalid or missing Authorization header","success":false}` |
| `500 Internal Server Error` | An unexpected error occurred on the server. | Varies. |

## Best Practices

- **Sequence Lengths**:
  - Protein: 10-1000 amino acids
  - DNA/RNA: 10-1000 bases
- **File Formats**:
  - Protein: PDB
  - Ligand: SDF, MOL2, MOL
  - SMILES: Standard SMILES strings
- **Performance**:
  - Use appropriate thread counts for AutoDock (1000-2000 recommended).
  - Use URLs for large input files to avoid large request bodies.
- **Task Management**:
  - Store the `task_id` for every submitted job. It is essential for tracking and retrieving results.

## Support

If you encounter any issues or have questions, please contact our technical support team code@quregenai.com or consult the detailed error logs available in the web interface.


#### 微信公众号 | Official WeChat
<img src="docs/images/wechat_offical_qrcode.jpg" alt="TyxonQ 微信公众号" width="200">

#### 开发者交流群 | Developer Community
<img src="docs/images/developer_group_qrcode.png" alt="TyxonQ 开发者交流群" width="200">

*扫码关注公众号获取最新资讯 | Scan to follow for latest updates*  
*扫码加入开发者群进行技术交流 | Scan to join developer community*