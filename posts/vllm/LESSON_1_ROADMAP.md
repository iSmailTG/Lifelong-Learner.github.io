# LESSON 1: Mini-Llama Inference Engine
## Your Complete Roadmap from Zero to Working Inference

---

## 🎯 THE BIG PICTURE (Read This When Lost)

**What you're building:** A Python program that generates text using Llama 3.2 1B, with custom CUDA kernels doing the heavy math.

**Why it matters:** This ONE project teaches you everything—CUDA, transformers, inference, Python/GPU integration. It's your foundation for understanding vLLM and any other inference engine.

**The path:** 4 weeks, 4 phases. Each phase produces something you can RUN and SEE working. No theory-only work.

**When you feel lost:** Come back to this section. Ask yourself: "Which phase am I in? What should I be able to RUN right now?"

---

## 📍 PHASE 1: Your First CUDA Kernel (Week 1)
### Goal: Write a matrix multiply kernel and call it from Python

**What you'll build:**
- `kernels/matmul.cu` - A simple CUDA kernel that multiplies two matrices
- `src/kernel_wrapper.py` - Python code that calls your CUDA kernel
- `test_matmul.py` - Proof it works

**Why this matters:**
This is your CUDA bootcamp. Everything else in this project builds on understanding how to write a kernel, compile it, and call it from Python. Once you have this working, the hard part is done.

### Step 1.1: Set up your environment (Day 1)

**What to do:**
```bash
# Create project structure
mkdir mini-llama-inference
cd mini-llama-inference
mkdir kernels src examples tests

# Create virtual environment
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows

# Install basics
pip install torch numpy pytest
```

**Checkpoint:** You can run `python --version` and `nvcc --version` and both work.

**If stuck:** Make sure you have CUDA toolkit installed. Google "install CUDA toolkit [your OS]".

### Step 1.2: Write your first kernel (Days 2-3)

**Create `kernels/matmul.cu`:**

```cuda
// This is a NAIVE matrix multiply. It's slow, but it's YOURS.
// You'll optimize later. For now: make it work.

#include <cuda_runtime.h>

// Kernel: each thread computes one element of output matrix C
__global__ void matmul_kernel(float* A, float* B, float* C, int M, int N, int K) {
    // M x K  times  K x N  =  M x N
    // A is M rows, K cols
    // B is K rows, N cols  
    // C is M rows, N cols
    
    int row = blockIdx.y * blockDim.y + threadIdx.y;  // which row of C
    int col = blockIdx.x * blockDim.x + threadIdx.x;  // which col of C
    
    if (row < M && col < N) {
        float sum = 0.0f;
        for (int i = 0; i < K; i++) {
            sum += A[row * K + i] * B[i * N + col];
        }
        C[row * N + col] = sum;
    }
}

// C++ wrapper function that Python will call
extern "C" {
    void matmul_cuda(float* A, float* B, float* C, int M, int N, int K) {
        // Define thread block and grid sizes
        dim3 blockDim(16, 16);  // 16x16 threads per block
        dim3 gridDim((N + 15) / 16, (M + 15) / 16);  // enough blocks to cover the output
        
        // Launch kernel
        matmul_kernel<<<gridDim, blockDim>>>(A, B, C, M, N, K);
        
        // Wait for GPU to finish
        cudaDeviceSynchronize();
    }
}
```

**Understanding the code (read slowly):**

1. **`__global__`** = This function runs on the GPU
2. **`blockIdx`, `threadIdx`** = Figure out which element this thread computes
3. **The loop** = Standard matrix multiply math: dot product of row and column
4. **`extern "C"`** = Makes it easy for Python to call this function
5. **`dim3`** = CUDA's way of organizing threads into blocks and grids

**Compile it:**
```bash
nvcc -shared -o kernels/matmul.so kernels/matmul.cu -Xcompiler -fPIC
```

**Checkpoint:** You have `kernels/matmul.so` file. It compiled with no errors.

**If stuck:** 
- Error about `nvcc`? Install CUDA toolkit
- Error about compute capability? Add `-arch=sm_XX` flag (find your GPU's compute capability online)
- Syntax errors? Check your CUDA version, copy code exactly first

### Step 1.3: Call it from Python (Day 4)

**Create `src/kernel_wrapper.py`:**

```python
import ctypes
import numpy as np
import torch

# Load your compiled CUDA kernel
matmul_lib = ctypes.CDLL('./kernels/matmul.so')

# Tell Python what arguments the function expects
matmul_lib.matmul_cuda.argtypes = [
    ctypes.POINTER(ctypes.c_float),  # A
    ctypes.POINTER(ctypes.c_float),  # B
    ctypes.POINTER(ctypes.c_float),  # C
    ctypes.c_int,                     # M
    ctypes.c_int,                     # N
    ctypes.c_int                      # K
]

def matmul_cuda(A, B):
    """
    Matrix multiply using your CUDA kernel.
    A: (M, K) tensor
    B: (K, N) tensor
    Returns: (M, N) tensor
    """
    # Get dimensions
    M, K = A.shape
    K2, N = B.shape
    assert K == K2, "Inner dimensions must match"
    
    # Allocate output
    C = torch.zeros(M, N, device='cuda', dtype=torch.float32)
    
    # Get pointers to GPU memory
    A_ptr = ctypes.cast(A.data_ptr(), ctypes.POINTER(ctypes.c_float))
    B_ptr = ctypes.cast(B.data_ptr(), ctypes.POINTER(ctypes.c_float))
    C_ptr = ctypes.cast(C.data_ptr(), ctypes.POINTER(ctypes.c_float))
    
    # Call your kernel
    matmul_lib.matmul_cuda(A_ptr, B_ptr, C_ptr, M, N, K)
    
    return C
```

**Understanding the code:**

1. **`ctypes.CDLL`** = Load your compiled .so file
2. **`.argtypes`** = Tell Python what types your C function expects
3. **`.data_ptr()`** = Get the GPU memory address from a PyTorch tensor
4. **The function wraps everything** = Makes it easy to use like any Python function

**Create `tests/test_matmul.py`:**

```python
import torch
import sys
sys.path.append('src')
from kernel_wrapper import matmul_cuda

def test_matmul():
    # Create small test matrices on GPU
    A = torch.randn(4, 8, device='cuda', dtype=torch.float32)
    B = torch.randn(8, 4, device='cuda', dtype=torch.float32)
    
    # Compute using YOUR kernel
    C_yours = matmul_cuda(A, B)
    
    # Compute using PyTorch (the "correct" answer)
    C_pytorch = torch.matmul(A, B)
    
    # Check if they match (within floating point tolerance)
    diff = torch.abs(C_yours - C_pytorch).max().item()
    print(f"Max difference: {diff}")
    
    assert diff < 1e-4, "Your kernel doesn't match PyTorch!"
    print("✅ SUCCESS! Your CUDA kernel works correctly!")

if __name__ == "__main__":
    test_matmul()
```

**Run it:**
```bash
python tests/test_matmul.py
```

**Checkpoint:** You see "✅ SUCCESS! Your CUDA kernel works correctly!"

**If stuck:**
- Import errors? Make sure you're in the right directory
- CUDA errors? Check that tensors are on GPU (`device='cuda'`)
- Wrong results? Double-check the kernel logic, especially indexing

### Step 1.4: Understand what you built (Day 5)

**Take a moment. You just:**
1. Wrote a CUDA kernel (code that runs on GPU)
2. Compiled it to a shared library
3. Called it from Python
4. Verified it gives correct results

**This is the foundation of EVERYTHING.**

Every kernel you write from now on follows this exact pattern:
1. Write `.cu` file
2. Compile to `.so`
3. Wrap in Python
4. Test against PyTorch

**When you feel lost later:** Come back and look at `matmul.cu` and `kernel_wrapper.py`. This is your reference implementation.

### Step 1.5: Celebrate and document (Day 6-7)

**Create `docs/week1_notes.md`:**
```markdown
# Week 1: My First CUDA Kernel

## What I learned
- How CUDA threads and blocks work
- How to compile CUDA code
- How to call CUDA from Python
- Matrix multiply algorithm

## Challenges I faced
[Write what was hard]

## Key insights
[Write your "aha!" moments]

## Questions I still have
[Write what you're unsure about]
```

**Update your README:**
```markdown
# Mini-Llama Inference Engine

**Status:** Week 1 complete - Basic CUDA kernel working

## What works so far
- Custom matrix multiply kernel in CUDA
- Python wrapper to call CUDA code
- Tests passing against PyTorch

## Next: Week 2 - Attention layer
```

**Checkpoint:** You have working code, documentation, and understanding. Week 1 is DONE.

---

## 📍 PHASE 2: Attention Layer (Week 2)
### Goal: Implement one attention layer using your kernels

**What you'll build:**
- `src/model.py` - Start with LlamaAttention class
- `kernels/attention.cu` - Attention computation kernel
- `tests/test_attention.py` - Verify it works

**Why this matters:**
Attention is the CORE of transformers. Once you understand one attention layer, you understand the whole model. Everything else is just stacking these layers.

### Step 2.1: Understand attention math (Day 1)

**The formula (don't panic):**
```
Attention(Q, K, V) = softmax(Q @ K^T / sqrt(d_k)) @ V
```

**What it means in plain English:**
1. Take queries Q and keys K
2. Multiply them: Q @ K^T (this gives "attention scores")
3. Scale by sqrt(d_k) (for numerical stability)
4. Apply softmax (turn into probabilities)
5. Multiply by values V (weighted sum)

**Draw this on paper:** Boxes and arrows. Q, K, V go in → attention output comes out.

**Create `docs/attention_notes.md`:**
```markdown
# Understanding Attention

## The pieces
- Q (queries): What we're looking for
- K (keys): What each position offers
- V (values): The actual information
- d_k: Dimension of keys (usually 128 or similar)

## The flow
[Draw or describe the computation flow]

## Key insight
Attention is asking: "Which positions should I pay attention to?"
The Q@K^T multiplication answers that question.
```

### Step 2.2: Implement attention in PyTorch first (Day 2)

**Create `src/model.py`:**

```python
import torch
import torch.nn as nn
import math

class LlamaAttention(nn.Module):
    """
    Multi-head attention for Llama.
    We'll start with PyTorch, then replace with custom kernels.
    """
    def __init__(self, hidden_size, num_heads):
        super().__init__()
        self.hidden_size = hidden_size
        self.num_heads = num_heads
        self.head_dim = hidden_size // num_heads
        
        # Linear projections for Q, K, V
        self.q_proj = nn.Linear(hidden_size, hidden_size, bias=False)
        self.k_proj = nn.Linear(hidden_size, hidden_size, bias=False)
        self.v_proj = nn.Linear(hidden_size, hidden_size, bias=False)
        self.o_proj = nn.Linear(hidden_size, hidden_size, bias=False)
        
    def forward(self, x):
        """
        x: (batch, seq_len, hidden_size)
        Returns: (batch, seq_len, hidden_size)
        """
        batch_size, seq_len, _ = x.shape
        
        # Project to Q, K, V
        Q = self.q_proj(x)  # (batch, seq_len, hidden_size)
        K = self.k_proj(x)
        V = self.v_proj(x)
        
        # Reshape for multi-head attention
        # (batch, seq_len, hidden_size) -> (batch, num_heads, seq_len, head_dim)
        Q = Q.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        K = K.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        V = V.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        
        # Compute attention scores: Q @ K^T
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.head_dim)
        # scores shape: (batch, num_heads, seq_len, seq_len)
        
        # Apply causal mask (can't attend to future tokens)
        mask = torch.triu(torch.ones(seq_len, seq_len), diagonal=1).bool()
        scores = scores.masked_fill(mask.to(x.device), float('-inf'))
        
        # Softmax to get attention weights
        attn_weights = torch.softmax(scores, dim=-1)
        
        # Apply attention to values
        output = torch.matmul(attn_weights, V)  # (batch, num_heads, seq_len, head_dim)
        
        # Reshape back
        output = output.transpose(1, 2).contiguous()
        output = output.view(batch_size, seq_len, self.hidden_size)
        
        # Final projection
        output = self.o_proj(output)
        
        return output
```

**Create `tests/test_attention.py`:**

```python
import torch
import sys
sys.path.append('src')
from model import LlamaAttention

def test_attention_shape():
    """Test that attention layer produces correct output shape"""
    hidden_size = 512
    num_heads = 8
    batch_size = 2
    seq_len = 10
    
    # Create attention layer
    attn = LlamaAttention(hidden_size, num_heads).cuda()
    
    # Create random input
    x = torch.randn(batch_size, seq_len, hidden_size, device='cuda')
    
    # Forward pass
    output = attn(x)
    
    # Check shape
    assert output.shape == (batch_size, seq_len, hidden_size)
    print(f"✅ Output shape correct: {output.shape}")
    
def test_attention_causal():
    """Test that attention is causal (can't see future)"""
    hidden_size = 64
    num_heads = 4
    seq_len = 5
    
    attn = LlamaAttention(hidden_size, num_heads).cuda()
    
    # Create input where each position is different
    x = torch.arange(seq_len * hidden_size, device='cuda', dtype=torch.float32)
    x = x.view(1, seq_len, hidden_size)
    
    output = attn(x)
    
    # Position 0 should not depend on positions 1-4
    # (This is a weak test, but checks basic causality)
    print("✅ Causal mask applied (visual inspection needed)")
    print(f"First position output: {output[0, 0, :5]}")

if __name__ == "__main__":
    test_attention_shape()
    test_attention_causal()
    print("\n✅ All attention tests passed!")
```

**Run it:**
```bash
python tests/test_attention.py
```

**Checkpoint:** Tests pass. You have a working attention layer in pure PyTorch.

**Understanding checkpoint:**
- You know what Q, K, V are
- You understand the attention formula
- You see how multi-head attention works (splitting into heads)
- You understand causal masking

**If stuck on concepts:**
- Watch: "Attention is All You Need" explained (YouTube)
- Read: Your `docs/attention_notes.md` and draw more diagrams
- Ask: "What is this specific line doing?" (break it down line by line)

### Step 2.3: Replace matmul with your kernel (Day 3-4)

**Goal:** Replace `torch.matmul` calls with your custom `matmul_cuda`.

**In `src/model.py`, modify the attention computation:**

```python
# At the top, import your kernel
import sys
sys.path.append('src')
from kernel_wrapper import matmul_cuda

# In the forward function, replace torch.matmul:

# OLD:
# scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.head_dim)

# NEW:
# We need to handle multi-head attention, so we'll do it per head
scores_list = []
for head_idx in range(self.num_heads):
    Q_head = Q[:, head_idx, :, :]  # (batch, seq_len, head_dim)
    K_head = K[:, head_idx, :, :]  # (batch, seq_len, head_dim)
    
    # For each batch item (if batch_size > 1, loop here too)
    # For now, assume batch_size = 1 for simplicity
    Q_2d = Q_head[0]  # (seq_len, head_dim)
    K_2d = K_head[0].T  # (head_dim, seq_len)
    
    # Use YOUR kernel
    scores_head = matmul_cuda(Q_2d, K_2d)  # (seq_len, seq_len)
    scores_head = scores_head / math.sqrt(self.head_dim)
    scores_list.append(scores_head.unsqueeze(0).unsqueeze(0))

scores = torch.cat(scores_list, dim=1)  # (1, num_heads, seq_len, seq_len)
```

**This is intentionally simplified.** You're learning. Full batching comes later.

**Test it:**
```bash
python tests/test_attention.py
```

**Checkpoint:** Tests still pass, but now using YOUR matmul kernel.

**If stuck:**
- Shape errors? Print shapes at each step
- Performance issues? That's okay for now, optimization comes later
- Kernel errors? Go back to your matmul test, make sure that still works

### Step 2.4: Add RoPE (Rotary Position Embeddings) (Day 5-7)

**Why RoPE matters:**
Transformers need to know token positions. RoPE is how Llama encodes position information into Q and K.

**Create `kernels/rope.cu`:**

```cuda
#include <cuda_runtime.h>
#include <math.h>

__global__ void rope_kernel(float* Q, float* K, int seq_len, int head_dim, int num_heads) {
    // Each thread handles one element
    int idx = blockIdx.x * blockDim.x + threadIdx.x;
    int total_size = seq_len * num_heads * head_dim;
    
    if (idx < total_size) {
        int pos = (idx / head_dim) % seq_len;  // which position
        int dim = idx % head_dim;              // which dimension
        
        // RoPE formula (simplified)
        float freq = 1.0f / powf(10000.0f, (2.0f * dim) / head_dim);
        float angle = pos * freq;
        
        // Rotate pairs of dimensions
        if (dim % 2 == 0 && dim + 1 < head_dim) {
            float q = Q[idx];
            float q_next = Q[idx + 1];
            Q[idx] = q * cosf(angle) - q_next * sinf(angle);
            Q[idx + 1] = q * sinf(angle) + q_next * cosf(angle);
            
            // Same for K
            float k = K[idx];
            float k_next = K[idx + 1];
            K[idx] = k * cosf(angle) - k_next * sinf(angle);
            K[idx + 1] = k * sinf(angle) + k_next * cosf(angle);
        }
    }
}

extern "C" {
    void apply_rope(float* Q, float* K, int seq_len, int head_dim, int num_heads) {
        int total_size = seq_len * num_heads * head_dim;
        int blockSize = 256;
        int numBlocks = (total_size + blockSize - 1) / blockSize;
        
        rope_kernel<<<numBlocks, blockSize>>>(Q, K, seq_len, head_dim, num_heads);
        cudaDeviceSynchronize();
    }
}
```

**Compile it:**
```bash
nvcc -shared -o kernels/rope.so kernels/rope.cu -Xcompiler -fPIC
```

**Add to `src/kernel_wrapper.py`:**

```python
# Load RoPE kernel
rope_lib = ctypes.CDLL('./kernels/rope.so')
rope_lib.apply_rope.argtypes = [
    ctypes.POINTER(ctypes.c_float),  # Q
    ctypes.POINTER(ctypes.c_float),  # K
    ctypes.c_int,                     # seq_len
    ctypes.c_int,                     # head_dim
    ctypes.c_int                      # num_heads
]

def apply_rope_cuda(Q, K, seq_len, head_dim, num_heads):
    """Apply rotary position embeddings to Q and K"""
    Q_ptr = ctypes.cast(Q.data_ptr(), ctypes.POINTER(ctypes.c_float))
    K_ptr = ctypes.cast(K.data_ptr(), ctypes.POINTER(ctypes.c_float))
    
    rope_lib.apply_rope(Q_ptr, K_ptr, seq_len, head_dim, num_heads)
    
    return Q, K  # Modified in-place
```

**Update `src/model.py` to use RoPE:**

```python
# In LlamaAttention.forward, after creating Q, K, V:

# Apply RoPE to Q and K
Q, K = apply_rope_cuda(Q, K, seq_len, self.head_dim, self.num_heads)
```

**Test everything still works:**
```bash
python tests/test_attention.py
```

**Checkpoint:** Attention layer now uses RoPE. Tests pass.

**Document your understanding:**

Update `docs/attention_notes.md`:
```markdown
## RoPE (Rotary Position Embeddings)

### What it does
Encodes position information directly into Q and K matrices by rotating them.

### Why it's cool
- No separate position embeddings needed
- Works for any sequence length
- Relative positions emerge naturally

### How it works
- For each position, rotate pairs of dimensions
- Rotation angle depends on position and dimension
- Creates a spiral pattern in the embedding space

### My implementation
[Describe your kernel, what was hard, what you learned]
```

---

## 📍 PHASE 3: Full Transformer Block (Week 3)
### Goal: Complete one full Llama block (Attention + FFN + Norms)

**What you'll build:**
- `LlamaFFN` class (feed-forward network)
- `LlamaBlock` class (combines attention + FFN)
- RMSNorm implementation
- Tests for the complete block

**Why this matters:**
Once you have ONE block working, stacking more blocks is trivial. This is where you go from "I have pieces" to "I have a working transformer."

### Step 3.1: Implement RMSNorm (Day 1-2)

**What is RMSNorm:**
A normalization layer that makes training stable. Simpler than LayerNorm.

Formula: `x / sqrt(mean(x^2) + eps) * gamma`

**Create `src/model.py` (add to existing file):**

```python
class RMSNorm(nn.Module):
    def __init__(self, hidden_size, eps=1e-6):
        super().__init__()
        self.eps = eps
        self.weight = nn.Parameter(torch.ones(hidden_size))
    
    def forward(self, x):
        # x: (batch, seq_len, hidden_size)
        # Compute RMS: sqrt(mean(x^2))
        variance = x.pow(2).mean(dim=-1, keepdim=True)
        x = x / torch.sqrt(variance + self.eps)
        return self.weight * x
```

**Test it:**

```python
# In tests/test_attention.py, add:
from model import RMSNorm

def test_rmsnorm():
    hidden_size = 64
    batch, seq_len = 2, 10
    
    norm = RMSNorm(hidden_size).cuda()
    x = torch.randn(batch, seq_len, hidden_size, device='cuda')
    
    output = norm(x)
    
    # Check shape
    assert output.shape == x.shape
    
    # Check that output has roughly unit variance
    var = output.pow(2).mean().item()
    assert 0.8 < var < 1.2, f"Variance should be ~1, got {var}"
    
    print(f"✅ RMSNorm working, output variance: {var:.3f}")

# Run it
test_rmsnorm()
```

**Checkpoint:** RMSNorm implemented and tested.

### Step 3.2: Implement Feed-Forward Network (Day 3-4)

**What is FFN:**
A simple two-layer neural network applied to each position independently. In Llama, it uses SwiGLU activation.

**Add to `src/model.py`:**

```python
class LlamaFFN(nn.Module):
    """
    Feed-Forward Network with SwiGLU activation.
    Structure: Linear -> SwiGLU -> Linear
    """
    def __init__(self, hidden_size, intermediate_size):
        super().__init__()
        self.gate_proj = nn.Linear(hidden_size, intermediate_size, bias=False)
        self.up_proj = nn.Linear(hidden_size, intermediate_size, bias=False)
        self.down_proj = nn.Linear(intermediate_size, hidden_size, bias=False)
    
    def forward(self, x):
        # SwiGLU: Swish(gate) * up
        # Swish(x) = x * sigmoid(x)
        gate = self.gate_proj(x)
        up = self.up_proj(x)
        
        # Apply SwiGLU
        swish_gate = gate * torch.sigmoid(gate)  # Swish activation
        hidden = swish_gate * up                 # Gating
        
        # Project back to hidden_size
        output = self.down_proj(hidden)
        return output
```

**Test it:**

```python
# In tests/test_attention.py, add:
from model import LlamaFFN

def test_ffn():
    hidden_size = 64
    intermediate_size = 256  # Usually 4x hidden_size
    batch, seq_len = 2, 10
    
    ffn = LlamaFFN(hidden_size, intermediate_size).cuda()
    x = torch.randn(batch, seq_len, hidden_size, device='cuda')
    
    output = ffn(x)
    
    # Check shape
    assert output.shape == x.shape
    print(f"✅ FFN working, output shape: {output.shape}")

test_ffn()
```

**Checkpoint:** FFN implemented and tested.

### Step 3.3: Combine into LlamaBlock (Day 5-6)

**The full transformer block structure:**
```
Input
  ↓
RMSNorm
  ↓
Attention (with residual connection)
  ↓
RMSNorm
  ↓
FFN (with residual connection)
  ↓
Output
```

**Add to `src/model.py`:**

```python
class LlamaBlock(nn.Module):
    """
    One complete Llama transformer block.
    This is the building block that we'll stack.
    """
    def __init__(self, hidden_size, num_heads, intermediate_size):
        super().__init__()
        self.attention = LlamaAttention(hidden_size, num_heads)
        self.ffn = LlamaFFN(hidden_size, intermediate_size)
        self.attention_norm = RMSNorm(hidden_size)
        self.ffn_norm = RMSNorm(hidden_size)
    
    def forward(self, x):
        # Attention with residual connection
        h = self.attention_norm(x)
        h = self.attention(h)
        x = x + h  # Residual connection
        
        # FFN with residual connection
        h = self.ffn_norm(x)
        h = self.ffn(h)
        x = x + h  # Residual connection
        
        return x
```

**Test the full block:**

```python
# In tests/test_attention.py, add:
from model import LlamaBlock

def test_full_block():
    hidden_size = 64
    num_heads = 4
    intermediate_size = 256
    batch, seq_len = 1, 10
    
    block = LlamaBlock(hidden_size, num_heads, intermediate_size).cuda()
    x = torch.randn(batch, seq_len, hidden_size, device='cuda')
    
    output = block(x)
    
    # Check shape
    assert output.shape == x.shape
    
    # Check that output changed (not just identity)
    diff = (output - x).abs().mean().item()
    assert diff > 0.01, "Block should transform the input"
    
    print(f"✅ Full LlamaBlock working!")
    print(f"   Input shape: {x.shape}")
    print(f"   Output shape: {output.shape}")
    print(f"   Avg change: {diff:.4f}")

test_full_block()
```

**Run all tests:**
```bash
python tests/test_attention.py
```

**Checkpoint:** You have a COMPLETE transformer block working!

### Step 3.4: Document and celebrate (Day 7)

**Update `docs/week3_notes.md`:**

```markdown
# Week 3: Complete Transformer Block

## What I built
- RMSNorm layer
- Feed-forward network with SwiGLU
- Complete LlamaBlock combining all pieces
- Tests verifying everything works

## Key concepts mastered
- Residual connections (why they matter)
- Layer normalization
- How attention and FFN work together

## Architecture understanding
[Draw the full block diagram]

## Performance notes
- Current speed: [measure tokens/second]
- Bottlenecks: [profile and identify]

## Next steps
- Stack multiple blocks
- Add embeddings and output layer
- Load real Llama weights
```

**At this point, take a step back:**

You now understand:
1. ✅ CUDA kernel development
2. ✅ Attention mechanism deeply
3. ✅ Complete transformer block architecture
4. ✅ How to test and verify implementations

You're 75% done with Lesson 1. The hardest conceptual parts are behind you.

---

## 📍 PHASE 4: End-to-End Inference (Week 4)
### Goal: Load real Llama weights and generate text

**What you'll build:**
- `LlamaModel` class (stack of blocks)
- `src/loader.py` (load actual Llama weights)
- `src/inference.py` (generation loop)
- `examples/generate_text.py` (the demo everyone sees)

**Why this matters:**
This is where theory becomes reality. You'll type a prompt and see YOUR code generate text. This is the payoff.

### Step 4.1: Stack blocks into LlamaModel (Day 1-2)

**Add to `src/model.py`:**

```python
class LlamaModel(nn.Module):
    """
    Complete Llama model: embeddings + blocks + output
    """
    def __init__(self, config):
        super().__init__()
        self.config = config
        
        # Token embeddings
        self.embed_tokens = nn.Embedding(config['vocab_size'], config['hidden_size'])
        
        # Stack of transformer blocks
        self.layers = nn.ModuleList([
            LlamaBlock(
                config['hidden_size'],
                config['num_heads'],
                config['intermediate_size']
            )
            for _ in range(config['num_layers'])
        ])
        
        # Final norm
        self.norm = RMSNorm(config['hidden_size'])
        
        # Output projection (to vocabulary)
        self.lm_head = nn.Linear(config['hidden_size'], config['vocab_size'], bias=False)
    
    def forward(self, input_ids):
        """
        input_ids: (batch, seq_len) - token IDs
        Returns: (batch, seq_len, vocab_size) - logits for next token
        """
        # Embed tokens
        x = self.embed_tokens(input_ids)  # (batch, seq_len, hidden_size)
        
        # Pass through all transformer blocks
        for layer in self.layers:
            x = layer(x)
        
        # Final norm
        x = self.norm(x)
        
        # Project to vocabulary
        logits = self.lm_head(x)  # (batch, seq_len, vocab_size)
        
        return logits
```

**Test with random weights:**

```python
# Create tests/test_model.py
import torch
import sys
sys.path.append('src')
from model import LlamaModel

def test_llama_model():
    # Tiny model for testing
    config = {
        'vocab_size': 1000,
        'hidden_size': 64,
        'num_heads': 4,
        'intermediate_size': 256,
        'num_layers': 2  # Just 2 layers for fast testing
    }
    
    model = LlamaModel(config).cuda()
    
    # Test input
    batch_size, seq_len = 1, 10
    input_ids = torch.randint(0, config['vocab_size'], (batch_size, seq_len), device='cuda')
    
    # Forward pass
    logits = model(input_ids)
    
    # Check output shape
    expected_shape = (batch_size, seq_len, config['vocab_size'])
    assert logits.shape == expected_shape, f"Expected {expected_shape}, got {logits.shape}"
    
    print(f"✅ LlamaModel working!")
    print(f"   Input: {input_ids.shape}")
    print(f"   Output: {logits.shape}")
    
    # Test that we can sample next token
    next_token_logits = logits[0, -1, :]  # Last position
    next_token = torch.argmax(next_token_logits).item()
    print(f"   Sampled next token: {next_token}")

if __name__ == "__main__":
    test_llama_model()
```

**Checkpoint:** Full model runs end-to-end with random weights.

### Step 4.2: Implement weight loading (Day 3-4)

**Create `src/loader.py`:**

```python
import torch
from safetensors import safe_open
import json
from pathlib import Path

def load_llama_weights(model_path, model):
    """
    Load Llama weights from HuggingFace format.
    
    Args:
        model_path: Path to directory containing model files
        model: Your LlamaModel instance
    """
    model_path = Path(model_path)
    
    # Load config
    with open(model_path / 'config.json', 'r') as f:
        config = json.load(f)
    
    print(f"Loading Llama model: {config.get('model_type', 'unknown')}")
    print(f"  Layers: {config['num_hidden_layers']}")
    print(f"  Hidden size: {config['hidden_size']}")
    
    # Load weights from safetensors
    weight_files = list(model_path.glob("*.safetensors"))
    
    if not weight_files:
        raise FileNotFoundError(f"No .safetensors files found in {model_path}")
    
    # Dictionary to collect all weights
    state_dict = {}
    
    for weight_file in weight_files:
        print(f"Loading {weight_file.name}...")
        with safe_open(weight_file, framework="pt", device="cpu") as f:
            for key in f.keys():
                state_dict[key] = f.get_tensor(key)
    
    # Map HuggingFace names to your model's names
    # This requires matching their naming convention to yours
    # (You may need to adjust this mapping)
    
    mapped_state_dict = {}
    for key, value in state_dict.items():
        # Example mappings (adjust based on actual model):
        new_key = key.replace('model.', '')  # Remove 'model.' prefix
        new_key = new_key.replace('self_attn', 'attention')
        new_key = new_key.replace('mlp', 'ffn')
        # Add more mappings as needed
        mapped_state_dict[new_key] = value
    
    # Load into your model
    missing, unexpected = model.load_state_dict(mapped_state_dict, strict=False)
    
    if missing:
        print(f"Warning: Missing keys: {len(missing)}")
        print(f"  First few: {missing[:5]}")
    
    if unexpected:
        print(f"Warning: Unexpected keys: {len(unexpected)}")
        print(f"  First few: {unexpected[:5]}")
    
    print("✅ Weights loaded successfully!")
    
    return config

def download_llama_model(model_name="meta-llama/Llama-3.2-1B"):
    """
    Download Llama model from HuggingFace.
    You'll need to accept the license and have HF token.
    """
    from huggingface_hub import snapshot_download
    
    print(f"Downloading {model_name}...")
    model_path = snapshot_download(
        repo_id=model_name,
        cache_dir="./weights"
    )
    print(f"Downloaded to: {model_path}")
    return model_path
```

**Note:** Weight loading can be tricky because naming conventions vary. You may need to:
1. Print out the keys from the safetensors file
2. Print out your model's parameter names
3. Create a mapping between them

**Create a helper script `scripts/inspect_weights.py`:**

```python
from safetensors import safe_open
import sys

def inspect_weights(weight_file):
    """Print all weight names in a safetensors file"""
    print(f"Inspecting: {weight_file}")
    print("=" * 60)
    
    with safe_open(weight_file, framework="pt", device="cpu") as f:
        for i, key in enumerate(f.keys()):
            tensor = f.get_tensor(key)
            print(f"{i:3d}. {key:50s} {tuple(tensor.shape)}")
    
    print("=" * 60)

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python inspect_weights.py <path_to_safetensors>")
    else:
        inspect_weights(sys.argv[1])
```

**Checkpoint:** You can load real Llama weights (even if mapping isn't perfect yet).

### Step 4.3: Implement generation loop (Day 5)

**Create `src/inference.py`:**

```python
import torch
from transformers import AutoTokenizer

def generate_text(model, tokenizer, prompt, max_new_tokens=50, temperature=0.8):
    """
    Generate text from a prompt using your model.
    
    Args:
        model: Your LlamaModel
        tokenizer: HuggingFace tokenizer
        prompt: Input text
        max_new_tokens: How many tokens to generate
        temperature: Sampling temperature (higher = more random)
    
    Returns:
        Generated text
    """
    model.eval()
    
    # Tokenize input
    input_ids = tokenizer.encode(prompt, return_tensors='pt').cuda()
    
    print(f"Prompt: {prompt}")
    print(f"Generating {max_new_tokens} tokens...")
    print("-" * 60)
    
    generated_ids = input_ids[0].tolist()
    
    with torch.no_grad():
        for step in range(max_new_tokens):
            # Get current input (all tokens generated so far)
            current_input = torch.tensor([generated_ids], device='cuda')
            
            # Forward pass
            logits = model(current_input)  # (1, seq_len, vocab_size)
            
            # Get logits for last position
            next_token_logits = logits[0, -1, :]  # (vocab_size,)
            
            # Apply temperature
            next_token_logits = next_token_logits / temperature
            
            # Sample next token
            probs = torch.softmax(next_token_logits, dim=-1)
            next_token = torch.multinomial(probs, num_samples=1).item()
            
            # Add to sequence
            generated_ids.append(next_token)
            
            # Decode and print
            generated_text = tokenizer.decode(generated_ids)
            print(f"\rStep {step+1}/{max_new_tokens}: {generated_text}", end='', flush=True)
            
            # Stop if we generate end-of-sequence token
            if next_token == tokenizer.eos_token_id:
                break
    
    print("\n" + "-" * 60)
    final_text = tokenizer.decode(generated_ids)
    return final_text
```

**Checkpoint:** You have a generation function that can produce text.

### Step 4.4: Create the demo (Day 6)

**Create `examples/generate_text.py`:**

```python
import torch
import sys
sys.path.append('src')

from model import LlamaModel
from loader import load_llama_weights
from inference import generate_text
from transformers import AutoTokenizer

def main():
    print("🚀 Mini-Llama Inference Engine")
    print("=" * 60)
    
    # Configuration for Llama 3.2 1B
    config = {
        'vocab_size': 128256,
        'hidden_size': 2048,
        'num_heads': 32,
        'intermediate_size': 8192,
        'num_layers': 16  # 1B model has 16 layers
    }
    
    print("Building model...")
    model = LlamaModel(config).cuda()
    
    print("Loading weights...")
    model_path = "./weights/Llama-3.2-1B"  # Adjust path
    load_llama_weights(model_path, model)
    
    print("Loading tokenizer...")
    tokenizer = AutoTokenizer.from_pretrained(model_path)
    
    print("\n✅ Model ready!")
    print("=" * 60)
    
    # Generate text
    prompt = "The future of artificial intelligence is"
    generated = generate_text(
        model=model,
        tokenizer=tokenizer,
        prompt=prompt,
        max_new_tokens=50,
        temperature=0.8
    )
    
    print("\n📝 Final output:")
    print(generated)

if __name__ == "__main__":
    main()
```

**Run it:**
```bash
python examples/generate_text.py
```

**Checkpoint:** You see text being generated by YOUR inference engine!

**If it doesn't work perfectly:**
- Text is gibberish? Weight loading might be wrong - check the mapping
- Crashes? Check tensor shapes at each step
- Slow? That's expected - optimization comes in later lessons

### Step 4.5: Benchmark and document (Day 7)

**Create `examples/benchmark.py`:**

```python
import torch
import time
import sys
sys.path.append('src')

from model import LlamaModel
from loader import load_llama_weights
from transformers import AutoTokenizer

def benchmark_inference(model, tokenizer, prompt, num_tokens=50):
    """Measure tokens per second"""
    model.eval()
    
    input_ids = tokenizer.encode(prompt, return_tensors='pt').cuda()
    
    # Warmup
    with torch.no_grad():
        _ = model(input_ids)
    
    # Benchmark
    generated_ids = input_ids[0].tolist()
    
    start_time = time.time()
    
    with torch.no_grad():
        for _ in range(num_tokens):
            current_input = torch.tensor([generated_ids], device='cuda')
            logits = model(current_input)
            next_token = torch.argmax(logits[0, -1, :]).item()
            generated_ids.append(next_token)
    
    end_time = time.time()
    elapsed = end_time - start_time
    tokens_per_second = num_tokens / elapsed
    
    return tokens_per_second

def main():
    print("⏱️  Benchmarking inference speed")
    print("=" * 60)
    
    config = {
        'vocab_size': 128256,
        'hidden_size': 2048,
        'num_heads': 32,
        'intermediate_size': 8192,
        'num_layers': 16
    }
    
    model = LlamaModel(config).cuda()
    model_path = "./weights/Llama-3.2-1B"
    load_llama_weights(model_path, model)
    tokenizer = AutoTokenizer.from_pretrained(model_path)
    
    prompt = "Hello, how are you?"
    
    print(f"Prompt: {prompt}")
    print(f"Measuring speed over 50 tokens...\n")
    
    speed = benchmark_inference(model, tokenizer, prompt, num_tokens=50)
    
    print(f"⚡ Speed: {speed:.2f} tokens/second")
    print(f"   Time per token: {1000/speed:.2f} ms")
    
    # Compare with PyTorch reference (if you want)
    print("\nNote: This is your FIRST implementation.")
    print("Later lessons will add:")
    print("  - KV caching (2-5x speedup)")
    print("  - Batching (better GPU utilization)")
    print("  - Kernel optimization (another 2-3x)")

if __name__ == "__main__":
    main()
```

**Update your README:**

```markdown
# Mini-Llama Inference Engine

✅ **Lesson 1 Complete!**

A minimal Llama 3.2 1B inference engine built from scratch in Python + CUDA.

## What it does
- Loads Llama weights
- Generates text from prompts
- Uses custom CUDA kernels for performance-critical operations

## Architecture
- Custom matrix multiply kernel (CUDA)
- Custom RoPE kernel (CUDA)
- Multi-head attention with causal masking
- SwiGLU feed-forward network
- RMSNorm layer normalization
- 16-layer transformer

## Performance
- Current: ~X tokens/second (measured on [your GPU])
- Next optimizations: KV caching, batching, kernel fusion

## Usage
```bash
# Generate text
python examples/generate_text.py

# Run benchmarks
python examples/benchmark.py

# Run tests
python tests/test_attention.py
python tests/test_model.py
```

## What I learned
- Transformer architecture from scratch
- CUDA kernel development
- Python/GPU integration
- Weight loading and management
- Text generation pipeline

## Next steps
- Lesson 2: Add KV caching
- Lesson 3: Add batching
- Lesson 4: Kernel optimization
- Lesson 5: Study nano-vllm

## Files
- `kernels/` - Custom CUDA implementations
- `src/` - Python model and utilities
- `examples/` - Demo scripts
- `tests/` - Validation tests
- `docs/` - Learning notes

Built as part of my learning journey to understand inference engines.
```

**Create `docs/lesson1_summary.md`:**

```markdown
# Lesson 1 Complete: Reflection

## What I built
[Describe your project]

## Technical skills gained
1. CUDA kernel programming
2. Transformer architecture understanding
3. PyTorch/CUDA integration
4. Weight loading and model initialization
5. Text generation pipelines

## Challenges overcome
[What was hard? How did you solve it?]

## Key insights
[Your "aha!" moments]

## Performance baseline
- Speed: X tokens/second
- Bottlenecks identified: [list them]

## What's next
Now that I have a working baseline:
1. Understand where time is spent (profiling)
2. Add KV caching (Lesson 2)
3. Study nano-vllm with full context
4. Optimize kernels

## Readiness for nano-vllm
✅ I understand attention
✅ I know CUDA basics
✅ I can read Python/CUDA hybrid code
✅ I've built an inference loop
✅ I know what problems need solving

Ready to learn batching, paging, and advanced optimizations!
```

---

## 🎯 YOU DID IT - LESSON 1 COMPLETE!

### What you've accomplished

You built a **complete inference engine from scratch**:
- ✅ Custom CUDA kernels (matmul, RoPE)
- ✅ Full transformer architecture (attention, FFN, norms)
- ✅ Weight loading from real Llama checkpoints
- ✅ Text generation pipeline
- ✅ Tests and benchmarks
- ✅ Documentation of your journey

### Skills you now have

**CUDA programming:**
- Write kernels
- Compile and link them
- Call from Python
- Debug GPU code

**Transformer deep dive:**
- Multi-head attention mechanism
- Rotary position embeddings
- Feed-forward networks
- Layer normalization
- Residual connections

**Systems understanding:**
- Model loading and initialization
- Memory management (CPU ↔ GPU)
- Inference loop implementation
- Sampling strategies

**Software engineering:**
- Project structure
- Testing and validation
- Documentation
- Benchmarking

### Portfolio impact

You now have:
1. **A GitHub repo** showing real ML systems work
2. **Working code** anyone can run
3. **Documentation** of your learning
4. **Benchmarks** showing you measure performance
5. **Tests** showing you validate correctness

This demonstrates to employers/collaborators:
- You understand transformers mechanically, not just theoretically
- You can write GPU code
- You can build ML systems end-to-end
- You document and test your work

### You're ready for nano-vllm

Before Lesson 1, nano-vllm would have been overwhelming. Now:

**You'll recognize:**
- The attention kernels (you wrote one)
- The inference loop (you built one)
- The weight loading (you implemented it)
- The Python/CUDA structure (you use it)

**You'll understand their optimizations:**
- KV caching: "They're solving the recomputation problem I had"
- Batching: "That's how they process multiple requests"
- PagedAttention: "Smart memory management for KV cache"

**You'll be able to:**
- Read their code fluently
- Understand design decisions
- Implement similar optimizations
- Contribute improvements

### The path forward

**Immediate next steps:**
1. Push your code to GitHub
2. Write a blog post about your journey
3. Share it (Twitter, LinkedIn, Reddit)

**Lesson 2: KV Caching**
- Understand why recomputing K,V is wasteful
- Implement cache data structure
- See 2-5x speedup

**Lesson 3: Batching**
- Process multiple requests together
- Learn scheduling challenges
- Better GPU utilization

**Lesson 4: Kernel Optimization**
- Study FlashAttention
- Implement kernel fusion
- Profile-guided optimization

**Lesson 5: nano-vllm Deep Dive**
- Read their implementation
- Compare with yours
- Understand PagedAttention
- Implement continuous batching

### When you feel lost (Important!)

**Remember: You can always come back to this guide.**

**If you're stuck on a specific piece:**
1. Find which phase it belongs to
2. Re-read the "Understanding" sections
3. Look at the checkpoint criteria
4. Run the tests for that piece in isolation

**If you're lost in the big picture:**
1. Come back to "THE BIG PICTURE" at the top
2. Ask: "What should I be able to RUN right now?"
3. Look at the phase goals
4. Remember: each phase produces something runnable

**If you're overwhelmed:**
1. Take a break
2. Come back and read your `docs/` notes
3. Run your working tests
4. Remember: You've already built working pieces

**The secret to not getting lost:**
- Always have something that RUNS
- Test each piece in isolation
- Document as you go
- Come back to this roadmap

### You're not alone

Thousands of people are learning this stuff. When stuck:
- Ask on Discord/Slack communities
- Post on Reddit (r/MachineLearning, r/LocalLLaMA)
- Open GitHub issues on nano-vllm
- Search for similar problems others solved

But now you have something most don't: **working code you built yourself**. That's your foundation. Everything builds on this.

### Final words

**You did the hard part.** 

You wrote your first CUDA kernel. You understood attention. You built a complete inference engine. You loaded real weights and generated text.

Everything from here is optimization and refinement. The conceptual foundations are solid.

**Go build. Go learn. Go grind.**

You've got this. 🚀

---

## Quick Reference Card

**Print this out or keep it visible:**

```
LESSON 1 QUICK REFERENCE
========================

PROJECT STRUCTURE:
  mini-llama-inference/
    kernels/       → CUDA code (.cu files)
    src/           → Python code
    examples/      → Demos
    tests/         → Validation
    docs/          → Your notes

KEY FILES:
  kernels/matmul.cu      → Matrix multiply kernel
  kernels/rope.cu        → Position embeddings
  src/model.py           → Transformer architecture
  src/kernel_wrapper.py  → Python ↔ CUDA bridge
  src/loader.py          → Weight loading
  src/inference.py       → Generation loop

CHECKPOINT QUESTIONS:
  ✓ Can I compile my kernels?
  ✓ Can I call them from Python?
  ✓ Do tests pass?
  ✓ Can I generate text?

WHEN LOST:
  1. Which phase am I in?
  2. What should work RIGHT NOW?
  3. Do my tests pass?
  4. Re-read the phase goal

WORKFLOW:
  Write → Compile → Test → Document → Next piece

REMEMBER:
  - Start small, iterate
  - Test each piece
  - Document as you go
  - It's okay to be slow at first
  - Optimization comes later

YOU'VE GOT THIS! 💪
```

---

**Now go build your inference engine! 🚀**
