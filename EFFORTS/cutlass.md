---
dg-publish: true
---

# CUTLASS Benchmarks (Volta, Ampere, Hopper, etc.)

CUTLASS is NVIDIA’s CUDA template library for GEMM and related operations. The suites include architecture-specific GEMM tests:

- **cutlass_volta**: cutlass_07_volta_tensorop_gemm
	 
- **cutlass_ampere**: cutlass_14_ampere_tf32_tensorop_gemm, cutlass_15_ampere_sparse_tensorop_gemm
	 
- **cutlass_hopper**: cutlass_50_hopper_gemm_with_epilogue_swizzle, cutlass_48_hopper_warp_specialized_gemm, cutlass_54_hopper_fp8_warp_specialized_gemm
	 
- **cutlass (general)**: cutlass_perf_test_k1, cutlass_05_batched_gemm, cutlass_41_fused_multi_head_attention_backward, cutlass_41_fused_multi_head_attention_fixed_seqlen, cutlass_41_fused_multi_head_attention_variable_seqlen (GEMM and attention kernels)

---
