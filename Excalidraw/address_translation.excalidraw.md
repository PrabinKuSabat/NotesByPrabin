---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠== You can decompress Drawing data with the command palette: 'Decompress current Excalidraw file'. For more info check in plugin settings under 'Saving'

# Excalidraw Data

## Text Elements
I/O Subsystem ^VNmA7MRf

DMA Device\nNIC  ·  GPU  ·  Storage ^LWfNEZyW

IOMMU\nIOVA → SPA\n2-stage for Virtualization\nStage-1 or Stage-2 = Bare = disabled ^Mq03NoUK

Issues IOVA ^Slgbmfk4

OS / Kernel ^j7iT2hhd

Page Fault Handler\nAllocate  ·  Swap  ·  Map\nUpdate page tables ^qlA7kI3A

Isolation\nDifferent processes\nsame VA → different PA ^7DGkWEyx

Sharing\nMultiple VAs\n→ same PA\nShared libs / SHM ^26XEuX9x

Physical Memory — DRAM ^70ZZ6KE6

Physical Address\nPPN  ⟶  Page Offset ^bYwv6reZ

Page Tables in DRAM\nOS-managed ^OlnM7F4C

CPU — MMU & Cache ^KPDppS8s

satp CSR\nRISC-V S-mode translation control\nSv39 / Sv48 / Sv57 / Bare ^UszUWP9C

L1–L3 Cache\nVIPT: Indexed by offset bits\nTagged by Physical Address\nOffset bits same in VA and PA ^pWYoXGA7

Controls translation mode\nAll S-mode fetches read satp implicitly ^sTKfLwJT

MMU — Memory Management Unit ^pIQ1KJwm

TLB Lookup\nSmall fast translation cache ^OliUeWyD

TLB Hit\nReturn PPN + Permissions ^1jpqHubW

TLB Miss\nPage Table Walk required ^kv4OtcJi

Permission Check\nR / W / X  ·  User vs Supervisor ^s1zZev3v

Multi-Level Page Table Walk\nSv39: 3 levels  ·  POWER: Radix / Hash\nEach level = 1 memory access ^BnUduY1L

TLB Refill\nCache the new translation ^n5sDTx4j

Hit ^ZKhUttKR

Miss ^V9LjL2qH

Translation found ^uGLBP5jA

Retry lookup ^wu8U3MVq

User Process ^TuVrLHIO

Program\nGenerates Virtual Address ^NWQfJX8v

Virtual Address\nVPN  ⟶  Page Offset ^MxlNYKOa

Generate VA ^mpKsy89U

Feed to MMU ^bp3PhUEN

Access Allowed ^LjyEOggx

Violation / Fault ^Qhya3V9Y

Page not present ^4bGx2Wzr

Cache Miss ^uvxC4nDv

Reads entries from ^m7lig4Ic

Updates ^24Ot98aN

Resume after fix ^2TAwV0rr

Translated SPA ^OYclLWCk

## Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.20.6",
	"elements": [
		{
			"id": "CxO-KBlg3t0D_mloa8tpY",
			"type": "rectangle",
			"x": 1505.1600142610248,
			"y": 805.3525185764114,
			"width": 330,
			"height": 520,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b00",
			"roundness": null,
			"seed": 1044738174,
			"version": 59,
			"versionNonce": 2138973886,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "VNmA7MRf"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "VNmA7MRf",
			"type": "text",
			"x": 1596.4900847566303,
			"y": 810.3525185764114,
			"width": 147.33985900878906,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b01",
			"roundness": null,
			"seed": 259961022,
			"version": 60,
			"versionNonce": 214343422,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "I/O Subsystem",
			"rawText": "I/O Subsystem",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "CxO-KBlg3t0D_mloa8tpY",
			"originalText": "I/O Subsystem",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "W8D7kb642bjDlIda2RH_4",
			"type": "rectangle",
			"x": 1540.1600142610248,
			"y": 875.3525185764114,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b02",
			"roundness": null,
			"seed": 539355390,
			"version": 60,
			"versionNonce": 47583038,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "LWfNEZyW"
				},
				{
					"id": "zZ6-sM_kfSyyR2VifBvLv",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "LWfNEZyW",
			"type": "text",
			"x": 1573.460093606728,
			"y": 895.3525185764114,
			"width": 193.39984130859375,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b03",
			"roundness": null,
			"seed": 1069291838,
			"version": 60,
			"versionNonce": 504199038,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "DMA Device\\nNIC  ·\nGPU  ·  Storage",
			"rawText": "DMA Device\\nNIC  ·  GPU  ·  Storage",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "W8D7kb642bjDlIda2RH_4",
			"originalText": "DMA Device\\nNIC  ·  GPU  ·  Storage",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "zC0eeUGXrIfPIn3iGtnfw",
			"type": "rectangle",
			"x": 1540.1600142610248,
			"y": 1105.3525185764115,
			"width": 260,
			"height": 180,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b04",
			"roundness": null,
			"seed": 631390590,
			"version": 61,
			"versionNonce": 1722668990,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "Mq03NoUK"
				},
				{
					"id": "zZ6-sM_kfSyyR2VifBvLv",
					"type": "arrow"
				},
				{
					"id": "Sb7r5vxy33IBULDMmRgxC",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "Mq03NoUK",
			"type": "text",
			"x": 1557.5501433503803,
			"y": 1132.8525185764115,
			"width": 225.21974182128906,
			"height": 125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b05",
			"roundness": null,
			"seed": 1964597694,
			"version": 60,
			"versionNonce": 886583294,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "IOMMU\\nIOVA →\nSPA\\n2-stage for\nVirtualization\\nStage-1\nor Stage-2 = Bare =\ndisabled",
			"rawText": "IOMMU\\nIOVA → SPA\\n2-stage for Virtualization\\nStage-1 or Stage-2 = Bare = disabled",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zC0eeUGXrIfPIn3iGtnfw",
			"originalText": "IOMMU\\nIOVA → SPA\\n2-stage for Virtualization\\nStage-1 or Stage-2 = Bare = disabled",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "zZ6-sM_kfSyyR2VifBvLv",
			"type": "arrow",
			"x": 1670.1600142610248,
			"y": 965.8525185764114,
			"width": 0,
			"height": 135,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b06",
			"roundness": {
				"type": 2
			},
			"seed": 1094875646,
			"version": 61,
			"versionNonce": 881688638,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "Slgbmfk4"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					84.5
				],
				[
					0,
					135
				]
			],
			"startBinding": {
				"elementId": "W8D7kb642bjDlIda2RH_4",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "zC0eeUGXrIfPIn3iGtnfw",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.022222222222222223
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "Slgbmfk4",
			"type": "text",
			"x": 1608.9700652253803,
			"y": 1037.8525185764115,
			"width": 122.37989807128906,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-zyo6Gp2p7TeX-77gu8bN"
			],
			"frameId": null,
			"index": "b07",
			"roundness": null,
			"seed": 287214142,
			"version": 60,
			"versionNonce": 1773062270,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Issues IOVA",
			"rawText": "Issues IOVA",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "zZ6-sM_kfSyyR2VifBvLv",
			"originalText": "Issues IOVA",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "dPOKWcNDqnnoEiecnRoL0",
			"type": "rectangle",
			"x": 1855.1600142610248,
			"y": 805.3525185764114,
			"width": 1035.203125,
			"height": 520,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b08",
			"roundness": null,
			"seed": 1670287998,
			"version": 59,
			"versionNonce": 872220862,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "j7iT2hhd"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "j7iT2hhd",
			"type": "text",
			"x": 2317.161624063271,
			"y": 810.3525185764114,
			"width": 111.19990539550781,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b09",
			"roundness": null,
			"seed": 861115070,
			"version": 60,
			"versionNonce": 1394163966,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "OS / Kernel",
			"rawText": "OS / Kernel",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "dPOKWcNDqnnoEiecnRoL0",
			"originalText": "OS / Kernel",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "9NR4Nz4sG7U7dv4tslz6G",
			"type": "rectangle",
			"x": 2402.410014261025,
			"y": 830.3525185764114,
			"width": 260,
			"height": 180,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0A",
			"roundness": null,
			"seed": 2064550654,
			"version": 63,
			"versionNonce": 1787064638,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "qlA7kI3A"
				},
				{
					"id": "DXhtp7gIOgWS2lFsNXZIV",
					"type": "arrow"
				},
				{
					"id": "hGEmxQmNtlgZHsc6tQnE8",
					"type": "arrow"
				},
				{
					"id": "Lfrw2J4x6XCBVREpBbC7c",
					"type": "arrow"
				},
				{
					"id": "VCe3msz8KTd3rO6CN5AVX",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "qlA7kI3A",
			"type": "text",
			"x": 2425.1101027620016,
			"y": 870.3525185764114,
			"width": 214.59982299804688,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0B",
			"roundness": null,
			"seed": 960527166,
			"version": 60,
			"versionNonce": 94345598,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Page Fault\nHandler\\nAllocate  ·\nSwap  ·  Map\\nUpdate\npage tables",
			"rawText": "Page Fault Handler\\nAllocate  ·  Swap  ·  Map\\nUpdate page tables",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "9NR4Nz4sG7U7dv4tslz6G",
			"originalText": "Page Fault Handler\\nAllocate  ·  Swap  ·  Map\\nUpdate page tables",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "PkPAdym9d6cJU7ssy7osQ",
			"type": "rectangle",
			"x": 1890.1600142610248,
			"y": 1135.3525185764115,
			"width": 260,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0C",
			"roundness": null,
			"seed": 1951032190,
			"version": 59,
			"versionNonce": 464239038,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "7DGkWEyx"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "7DGkWEyx",
			"type": "text",
			"x": 1908.7801085603412,
			"y": 1157.8525185764115,
			"width": 222.7598114013672,
			"height": 75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0D",
			"roundness": null,
			"seed": 1047348158,
			"version": 60,
			"versionNonce": 331993598,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Isolation\\nDifferent\nprocesses\\nsame VA →\ndifferent PA",
			"rawText": "Isolation\\nDifferent processes\\nsame VA → different PA",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "PkPAdym9d6cJU7ssy7osQ",
			"originalText": "Isolation\\nDifferent processes\\nsame VA → different PA",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Xd4PEM1tVQl7BY6I2YUIJ",
			"type": "rectangle",
			"x": 2200.160014261025,
			"y": 1120.3525185764115,
			"width": 260,
			"height": 150,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0E",
			"roundness": null,
			"seed": 969546750,
			"version": 59,
			"versionNonce": 1224401470,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "26XEuX9x"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "26XEuX9x",
			"type": "text",
			"x": 2205.3801222932516,
			"y": 1157.8525185764115,
			"width": 249.55978393554688,
			"height": 75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"5srpT39RSms4lj0oblw6R"
			],
			"frameId": null,
			"index": "b0F",
			"roundness": null,
			"seed": 476179518,
			"version": 60,
			"versionNonce": 236972670,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Sharing\\nMultiple VAs\\n→\nsame PA\\nShared libs /\nSHM",
			"rawText": "Sharing\\nMultiple VAs\\n→ same PA\\nShared libs / SHM",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Xd4PEM1tVQl7BY6I2YUIJ",
			"originalText": "Sharing\\nMultiple VAs\\n→ same PA\\nShared libs / SHM",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "cV6a09aVVtlJeeEk3p9Uz",
			"type": "rectangle",
			"x": 1505.1600142610248,
			"y": 1405.3525185764115,
			"width": 1192.25,
			"height": 195,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0G",
			"roundness": null,
			"seed": 873837694,
			"version": 59,
			"versionNonce": 838462142,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "70ZZ6KE6"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "70ZZ6KE6",
			"type": "text",
			"x": 1976.4051085603412,
			"y": 1410.3525185764115,
			"width": 249.7598114013672,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0H",
			"roundness": null,
			"seed": 1789293758,
			"version": 60,
			"versionNonce": 53076734,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Physical Memory — DRAM",
			"rawText": "Physical Memory — DRAM",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "cV6a09aVVtlJeeEk3p9Uz",
			"originalText": "Physical Memory — DRAM",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "jyIvod1ZI50nIjtrMFEqj",
			"type": "rectangle",
			"x": 1540.1600142610248,
			"y": 1430.3525185764115,
			"width": 260,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0I",
			"roundness": null,
			"seed": 1236610302,
			"version": 61,
			"versionNonce": 1412485950,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "bYwv6reZ"
				},
				{
					"id": "_3QWh3S4m3Odn59ZR1ow1",
					"type": "arrow"
				},
				{
					"id": "Sb7r5vxy33IBULDMmRgxC",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "bYwv6reZ",
			"type": "text",
			"x": 1556.85011588456,
			"y": 1465.3525185764115,
			"width": 226.6197967529297,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0J",
			"roundness": null,
			"seed": 2126699838,
			"version": 60,
			"versionNonce": 2138028926,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Physical Address\\nPPN\n⟶  Page Offset",
			"rawText": "Physical Address\\nPPN  ⟶  Page Offset",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "jyIvod1ZI50nIjtrMFEqj",
			"originalText": "Physical Address\\nPPN  ⟶  Page Offset",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "RHl0PZnY70DizqJQR5Cjf",
			"type": "rectangle",
			"x": 2402.410014261025,
			"y": 1445.3525185764115,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0K",
			"roundness": null,
			"seed": 1597015422,
			"version": 61,
			"versionNonce": 1797757886,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "OlnM7F4C"
				},
				{
					"id": "YyMC3wfODR1j8yZ87SAz7",
					"type": "arrow"
				},
				{
					"id": "Lfrw2J4x6XCBVREpBbC7c",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "OlnM7F4C",
			"type": "text",
			"x": 2433.300105203408,
			"y": 1465.3525185764115,
			"width": 198.21981811523438,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"-Aht3tqbkaG6cwke1Lhym"
			],
			"frameId": null,
			"index": "b0L",
			"roundness": null,
			"seed": 1816077758,
			"version": 60,
			"versionNonce": 226063358,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Page Tables in\nDRAM\\nOS-managed",
			"rawText": "Page Tables in DRAM\\nOS-managed",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "RHl0PZnY70DizqJQR5Cjf",
			"originalText": "Page Tables in DRAM\\nOS-managed",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Wcwccchrb_hx0IMIZooNV",
			"type": "rectangle",
			"x": 2910.363139261025,
			"y": -529.6474814235886,
			"width": 1109.4296875,
			"height": 2130,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0M",
			"roundness": null,
			"seed": 1460649470,
			"version": 59,
			"versionNonce": 1885750334,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "KPDppS8s"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "KPDppS8s",
			"type": "text",
			"x": 3361.2280532014547,
			"y": -524.6474814235886,
			"width": 207.69985961914062,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0N",
			"roundness": null,
			"seed": 77811262,
			"version": 60,
			"versionNonce": 902761598,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "CPU — MMU & Cache",
			"rawText": "CPU — MMU & Cache",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "Wcwccchrb_hx0IMIZooNV",
			"originalText": "CPU — MMU & Cache",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "unw7OKotG7kRuHdKCERVo",
			"type": "rectangle",
			"x": 3707.292826761025,
			"y": -504.6474814235886,
			"width": 260,
			"height": 150,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0O",
			"roundness": null,
			"seed": 299554430,
			"version": 60,
			"versionNonce": 460639422,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "UszUWP9C"
				},
				{
					"id": "aKwylzMXGlxl4MB_9G03e",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "UszUWP9C",
			"type": "text",
			"x": 3721.202952493447,
			"y": -479.6474814235886,
			"width": 232.17974853515625,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0P",
			"roundness": null,
			"seed": 1467438782,
			"version": 60,
			"versionNonce": 2130216190,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "satp CSR\\nRISC-V S-\nmode translation\ncontrol\\nSv39 / Sv48 /\nSv57 / Bare",
			"rawText": "satp CSR\\nRISC-V S-mode translation control\\nSv39 / Sv48 / Sv57 / Bare",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "unw7OKotG7kRuHdKCERVo",
			"originalText": "satp CSR\\nRISC-V S-mode translation control\\nSv39 / Sv48 / Sv57 / Bare",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "cZpYrGFOK3vINQ59E6ZBm",
			"type": "rectangle",
			"x": 2945.363139261025,
			"y": 1090.3525185764115,
			"width": 260,
			"height": 210,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0Q",
			"roundness": null,
			"seed": 1847185150,
			"version": 61,
			"versionNonce": 1641755966,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "pWYoXGA7"
				},
				{
					"id": "cfaOjAPG5t85gVL_AhXlU",
					"type": "arrow"
				},
				{
					"id": "_3QWh3S4m3Odn59ZR1ow1",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "pWYoXGA7",
			"type": "text",
			"x": 2951.6932555329977,
			"y": 1132.8525185764115,
			"width": 247.3397674560547,
			"height": 125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0R",
			"roundness": null,
			"seed": 843267902,
			"version": 60,
			"versionNonce": 1836965246,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "L1–L3 Cache\\nVIPT:\nIndexed by offset\nbits\\nTagged by Physical\nAddress\\nOffset bits\nsame in VA and PA",
			"rawText": "L1–L3 Cache\\nVIPT: Indexed by offset bits\\nTagged by Physical Address\\nOffset bits same in VA and PA",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "cZpYrGFOK3vINQ59E6ZBm",
			"originalText": "L1–L3 Cache\\nVIPT: Indexed by offset bits\\nTagged by Physical Address\\nOffset bits same in VA and PA",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "aKwylzMXGlxl4MB_9G03e",
			"type": "arrow",
			"x": 3837.293014261025,
			"y": -354.1474814235886,
			"width": 0,
			"height": 190,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "dashed",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0S",
			"roundness": {
				"type": 2
			},
			"seed": 1213507454,
			"version": 61,
			"versionNonce": 1998683582,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "sTKfLwJT"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					24.5
				],
				[
					0,
					109.5
				],
				[
					0,
					190
				]
			],
			"startBinding": {
				"elementId": "unw7OKotG7kRuHdKCERVo",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "wmN1RakmPKntisrsyxS7o",
				"mode": "orbit",
				"fixedPoint": [
					0.7860238871686193,
					-0.0023054755043227667
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "sTKfLwJT",
			"type": "text",
			"x": 3739.4731290071186,
			"y": -337.1510951503557,
			"width": 195.6397705078125,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0T",
			"roundness": null,
			"seed": 546244542,
			"version": 61,
			"versionNonce": 65476094,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Controls translation\nmode\\nAll S-mode\nfetches read satp\nimplicitly",
			"rawText": "Controls translation mode\\nAll S-mode fetches read satp implicitly",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "aKwylzMXGlxl4MB_9G03e",
			"originalText": "Controls translation mode\\nAll S-mode fetches read satp implicitly",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "wmN1RakmPKntisrsyxS7o",
			"type": "rectangle",
			"x": 3240.363139261025,
			"y": -159.6474814235886,
			"width": 759.4296875,
			"height": 1735,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0U",
			"roundness": null,
			"seed": 2064867326,
			"version": 60,
			"versionNonce": 1288676926,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "pIQ1KJwm"
				},
				{
					"id": "aKwylzMXGlxl4MB_9G03e",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "pIQ1KJwm",
			"type": "text",
			"x": 3458.3481246125875,
			"y": -154.6474814235886,
			"width": 323.459716796875,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0V",
			"roundness": null,
			"seed": 1810018366,
			"version": 60,
			"versionNonce": 1119628926,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "MMU — Memory Management Unit",
			"rawText": "MMU — Memory Management Unit",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "wmN1RakmPKntisrsyxS7o",
			"originalText": "MMU — Memory Management Unit",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "7duO4d1oxkCrNRIra6lOK",
			"type": "diamond",
			"x": 3569.722514261025,
			"y": -134.6474814235886,
			"width": 290,
			"height": 290,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0W",
			"roundness": null,
			"seed": 1591605374,
			"version": 63,
			"versionNonce": 309178046,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "OliUeWyD"
				},
				{
					"id": "YEUIt3762z_g2LqBOF5OG",
					"type": "arrow"
				},
				{
					"id": "Txz3GYNZx7RcosexnEyDu",
					"type": "arrow"
				},
				{
					"id": "m_ZxU7dPJjihOtHLh-6SD",
					"type": "arrow"
				},
				{
					"id": "PxwslPXttYGTEzyL4-hQ9",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "OliUeWyD",
			"type": "text",
			"x": 3649.102580178994,
			"y": -52.1474814235886,
			"width": 131.2398681640625,
			"height": 125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0X",
			"roundness": null,
			"seed": 465975486,
			"version": 60,
			"versionNonce": 679814910,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "TLB\nLookup\\nSmal\nl fast\ntranslation\ncache",
			"rawText": "TLB Lookup\\nSmall fast translation cache",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "7duO4d1oxkCrNRIra6lOK",
			"originalText": "TLB Lookup\\nSmall fast translation cache",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "M_sJyCa6UbYFD3lZ3bk5g",
			"type": "rectangle",
			"x": 3305.363139261025,
			"y": 235.3525185764114,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0Y",
			"roundness": null,
			"seed": 1245475070,
			"version": 61,
			"versionNonce": 897816382,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "1jpqHubW"
				},
				{
					"id": "Txz3GYNZx7RcosexnEyDu",
					"type": "arrow"
				},
				{
					"id": "5wxHuyJo9OVeIrUzOpe06",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "1jpqHubW",
			"type": "text",
			"x": 3318.0032530915914,
			"y": 255.3525185764114,
			"width": 234.7197723388672,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0Z",
			"roundness": null,
			"seed": 1975623998,
			"version": 60,
			"versionNonce": 835321726,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "TLB Hit\\nReturn PPN +\nPermissions",
			"rawText": "TLB Hit\\nReturn PPN + Permissions",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "M_sJyCa6UbYFD3lZ3bk5g",
			"originalText": "TLB Hit\\nReturn PPN + Permissions",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "5FELUmzA3ygcMDfrcm8n1",
			"type": "rectangle",
			"x": 3584.222514261025,
			"y": 875.3525185764114,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0a",
			"roundness": null,
			"seed": 297191806,
			"version": 61,
			"versionNonce": 1637441470,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "kv4OtcJi"
				},
				{
					"id": "m_ZxU7dPJjihOtHLh-6SD",
					"type": "arrow"
				},
				{
					"id": "MQlkBCizwCpcAo-VooRAV",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "kv4OtcJi",
			"type": "text",
			"x": 3602.742617715615,
			"y": 895.3525185764114,
			"width": 222.9597930908203,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0b",
			"roundness": null,
			"seed": 2103241150,
			"version": 60,
			"versionNonce": 1494398974,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "TLB Miss\\nPage Table\nWalk required",
			"rawText": "TLB Miss\\nPage Table Walk required",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "5FELUmzA3ygcMDfrcm8n1",
			"originalText": "TLB Miss\\nPage Table Walk required",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "NqWYoFzj1pCtsBG_3abhd",
			"type": "diamond",
			"x": 3275.863139261025,
			"y": 405.3525185764114,
			"width": 320,
			"height": 320,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0c",
			"roundness": null,
			"seed": 131400190,
			"version": 62,
			"versionNonce": 3606590,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "s1zZev3v"
				},
				{
					"id": "5wxHuyJo9OVeIrUzOpe06",
					"type": "arrow"
				},
				{
					"id": "cfaOjAPG5t85gVL_AhXlU",
					"type": "arrow"
				},
				{
					"id": "DXhtp7gIOgWS2lFsNXZIV",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "s1zZev3v",
			"type": "text",
			"x": 3365.0731917512594,
			"y": 515.3525185764114,
			"width": 141.57989501953125,
			"height": 100,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0d",
			"roundness": null,
			"seed": 1948258878,
			"version": 60,
			"versionNonce": 384258174,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Permission\nCheck\\nR / W\n/ X  ·  User vs\nSupervisor",
			"rawText": "Permission Check\\nR / W / X  ·  User vs Supervisor",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "NqWYoFzj1pCtsBG_3abhd",
			"originalText": "Permission Check\\nR / W / X  ·  User vs Supervisor",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "2LaQP3JdiOX__79H6zTUj",
			"type": "rectangle",
			"x": 3527.574076761025,
			"y": 1090.3525185764115,
			"width": 260,
			"height": 210,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0e",
			"roundness": null,
			"seed": 2099190398,
			"version": 64,
			"versionNonce": 954503358,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "BnUduY1L"
				},
				{
					"id": "MQlkBCizwCpcAo-VooRAV",
					"type": "arrow"
				},
				{
					"id": "UUMjhexYAuimbz2TCgoKL",
					"type": "arrow"
				},
				{
					"id": "hGEmxQmNtlgZHsc6tQnE8",
					"type": "arrow"
				},
				{
					"id": "YyMC3wfODR1j8yZ87SAz7",
					"type": "arrow"
				},
				{
					"id": "VCe3msz8KTd3rO6CN5AVX",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "BnUduY1L",
			"type": "text",
			"x": 3548.674174417275,
			"y": 1132.8525185764115,
			"width": 217.7998046875,
			"height": 125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0f",
			"roundness": null,
			"seed": 1502812862,
			"version": 60,
			"versionNonce": 1641007358,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Multi-Level Page Table\nWalk\\nSv39: 3 levels  ·\nPOWER: Radix /\nHash\\nEach level = 1\nmemory access",
			"rawText": "Multi-Level Page Table Walk\\nSv39: 3 levels  ·  POWER: Radix / Hash\\nEach level = 1 memory access",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "2LaQP3JdiOX__79H6zTUj",
			"originalText": "Multi-Level Page Table Walk\\nSv39: 3 levels  ·  POWER: Radix / Hash\\nEach level = 1 memory access",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "CqLIwD-s8L-PYGfgbqUH7",
			"type": "rectangle",
			"x": 3700.769389261025,
			"y": 1445.3525185764115,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0g",
			"roundness": null,
			"seed": 1075647230,
			"version": 61,
			"versionNonce": 1898576190,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "n5sDTx4j"
				},
				{
					"id": "UUMjhexYAuimbz2TCgoKL",
					"type": "arrow"
				},
				{
					"id": "PxwslPXttYGTEzyL4-hQ9",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "n5sDTx4j",
			"type": "text",
			"x": 3720.669489969033,
			"y": 1465.3525185764115,
			"width": 220.19979858398438,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0h",
			"roundness": null,
			"seed": 1799226174,
			"version": 60,
			"versionNonce": 777265534,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "TLB Refill\\nCache the\nnew translation",
			"rawText": "TLB Refill\\nCache the new translation",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "CqLIwD-s8L-PYGfgbqUH7",
			"originalText": "TLB Refill\\nCache the new translation",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Txz3GYNZx7RcosexnEyDu",
			"type": "arrow",
			"x": 3627.053014261025,
			"y": 68.68251857641144,
			"width": 191.69000000000005,
			"height": 162.16999999999996,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0i",
			"roundness": {
				"type": 2
			},
			"seed": 492235646,
			"version": 61,
			"versionNonce": 1284456894,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "ZKhUttKR"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-191.69000000000005,
					126.66999999999996
				],
				[
					-191.69000000000005,
					162.16999999999996
				]
			],
			"startBinding": {
				"elementId": "7duO4d1oxkCrNRIra6lOK",
				"mode": "orbit",
				"fixedPoint": [
					0.19769137931034492,
					0.6994137931034484
				]
			},
			"endBinding": {
				"elementId": "M_sJyCa6UbYFD3lZ3bk5g",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.044444444444444446
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "ZKhUttKR",
			"type": "text",
			"x": 3421.6630325715714,
			"y": 182.8525185764114,
			"width": 27.39996337890625,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0j",
			"roundness": null,
			"seed": 615041982,
			"version": 60,
			"versionNonce": 1862031870,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Hit",
			"rawText": "Hit",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Txz3GYNZx7RcosexnEyDu",
			"originalText": "Hit",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "m_ZxU7dPJjihOtHLh-6SD",
			"type": "arrow",
			"x": 3714.223014261025,
			"y": 155.8525185764114,
			"width": 0,
			"height": 715,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0k",
			"roundness": {
				"type": 2
			},
			"seed": 1599382526,
			"version": 61,
			"versionNonce": 748448318,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "V9LjL2qH"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					39.5
				],
				[
					0,
					124.5
				],
				[
					0,
					209.5
				],
				[
					0,
					409.5
				],
				[
					0,
					609.5
				],
				[
					0,
					649.5
				],
				[
					0,
					715
				]
			],
			"startBinding": {
				"elementId": "7duO4d1oxkCrNRIra6lOK",
				"mode": "orbit",
				"fixedPoint": [
					0.4982775862068969,
					1
				]
			},
			"endBinding": {
				"elementId": "5FELUmzA3ygcMDfrcm8n1",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.044444444444444446
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "V9LjL2qH",
			"type": "text",
			"x": 3693.263038064736,
			"y": 452.8652877912567,
			"width": 41.919952392578125,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0l",
			"roundness": null,
			"seed": 1062958142,
			"version": 60,
			"versionNonce": 340816510,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Miss",
			"rawText": "Miss",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "m_ZxU7dPJjihOtHLh-6SD",
			"originalText": "Miss",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "5wxHuyJo9OVeIrUzOpe06",
			"type": "arrow",
			"x": 3435.3630142610245,
			"y": 325.8525185764114,
			"width": 0,
			"height": 75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0m",
			"roundness": {
				"type": 2
			},
			"seed": 1318993022,
			"version": 61,
			"versionNonce": 819557054,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					39.5
				],
				[
					0,
					75
				]
			],
			"startBinding": {
				"elementId": "M_sJyCa6UbYFD3lZ3bk5g",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "NqWYoFzj1pCtsBG_3abhd",
				"mode": "orbit",
				"fixedPoint": [
					0.4984371093749999,
					-0.0125
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "MQlkBCizwCpcAo-VooRAV",
			"type": "arrow",
			"x": 3713.723014261025,
			"y": 965.8525185764114,
			"width": 13.672000000000025,
			"height": 120.27399999999989,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0n",
			"roundness": {
				"type": 2
			},
			"seed": 853408958,
			"version": 61,
			"versionNonce": 215509758,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.5,
					84.5
				],
				[
					-13.172000000000025,
					120.27399999999989
				]
			],
			"startBinding": {
				"elementId": "5FELUmzA3ygcMDfrcm8n1",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "2LaQP3JdiOX__79H6zTUj",
				"mode": "orbit",
				"fixedPoint": [
					0.6633728365384618,
					-0.01774285714285768
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "UUMjhexYAuimbz2TCgoKL",
			"type": "arrow",
			"x": 3731.3280142610247,
			"y": 1300.8525185764115,
			"width": 52.478999999999814,
			"height": 141.1300000000001,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0o",
			"roundness": {
				"type": 2
			},
			"seed": 834385150,
			"version": 61,
			"versionNonce": 919493438,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "uGLBP5jA"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					16.940999999999804,
					24.5
				],
				[
					16.940999999999804,
					64.5
				],
				[
					16.940999999999804,
					104.5
				],
				[
					52.478999999999814,
					141.1300000000001
				]
			],
			"startBinding": {
				"elementId": "2LaQP3JdiOX__79H6zTUj",
				"mode": "orbit",
				"fixedPoint": [
					0.7817459134615389,
					1
				]
			},
			"endBinding": {
				"elementId": "CqLIwD-s8L-PYGfgbqUH7",
				"mode": "orbit",
				"fixedPoint": [
					0.3212985576923074,
					-0.031888888888887676
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "uGLBP5jA",
			"type": "text",
			"x": 3661.329111001747,
			"y": 1352.8525185764115,
			"width": 173.8798065185547,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0p",
			"roundness": null,
			"seed": 884499774,
			"version": 60,
			"versionNonce": 1159480190,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Translation found",
			"rawText": "Translation found",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "UUMjhexYAuimbz2TCgoKL",
			"originalText": "Translation found",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "PxwslPXttYGTEzyL4-hQ9",
			"type": "arrow",
			"x": 3855.9210142610245,
			"y": 1444.8525185764115,
			"width": 93.48100000000022,
			"height": 1354.3719999999998,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0q",
			"roundness": {
				"type": 2
			},
			"seed": 1295322494,
			"version": 61,
			"versionNonce": 1510155198,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "wu8U3MVq"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					23.302000000000135,
					-39.5
				],
				[
					23.302000000000135,
					-79.5
				],
				[
					23.302000000000135,
					-119.5
				],
				[
					23.302000000000135,
					-249.5
				],
				[
					23.302000000000135,
					-394.5
				],
				[
					23.302000000000135,
					-524.5
				],
				[
					23.302000000000135,
					-639.5
				],
				[
					23.302000000000135,
					-679.5
				],
				[
					23.302000000000135,
					-879.5
				],
				[
					23.302000000000135,
					-1079.5
				],
				[
					23.302000000000135,
					-1164.5
				],
				[
					23.302000000000135,
					-1249.5
				],
				[
					-70.17900000000009,
					-1354.3719999999998
				]
			],
			"startBinding": {
				"elementId": "CqLIwD-s8L-PYGfgbqUH7",
				"mode": "orbit",
				"fixedPoint": [
					0.5986600961538461,
					0
				]
			},
			"endBinding": {
				"elementId": "7duO4d1oxkCrNRIra6lOK",
				"mode": "orbit",
				"fixedPoint": [
					0.743170689655172,
					0.7745793103448281
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "wu8U3MVq",
			"type": "text",
			"x": 3817.11307468583,
			"y": 850.3490310625343,
			"width": 124.21987915039062,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"3Vie65dGMrdjojN-G9LbT",
				"6x3-RNlzvMrvoNHBFUpYO"
			],
			"frameId": null,
			"index": "b0r",
			"roundness": null,
			"seed": 947039678,
			"version": 61,
			"versionNonce": 665058302,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Retry lookup",
			"rawText": "Retry lookup",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "PxwslPXttYGTEzyL4-hQ9",
			"originalText": "Retry lookup",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Xh19eSimL2Z_5ay8iqZza",
			"type": "rectangle",
			"x": 2025.2615767610248,
			"y": -724.6474814235886,
			"width": 330,
			"height": 395,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0s",
			"roundness": null,
			"seed": 1670530558,
			"version": 59,
			"versionNonce": 1946143806,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "TuVrLHIO"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "TuVrLHIO",
			"type": "text",
			"x": 2125.6716414582907,
			"y": -719.6474814235886,
			"width": 129.17987060546875,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0t",
			"roundness": null,
			"seed": 1872025150,
			"version": 60,
			"versionNonce": 485987454,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "User Process",
			"rawText": "User Process",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "top",
			"containerId": "Xh19eSimL2Z_5ay8iqZza",
			"originalText": "User Process",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "1y9ilH22jwQTnqlIlN-Bw",
			"type": "rectangle",
			"x": 2060.261576761025,
			"y": -699.6474814235886,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0u",
			"roundness": null,
			"seed": 895180414,
			"version": 60,
			"versionNonce": 474764478,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "NWQfJX8v"
				},
				{
					"id": "f6seSFs1MewwAAOy7jbQZ",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "NWQfJX8v",
			"type": "text",
			"x": 2090.7416793000875,
			"y": -679.6474814235886,
			"width": 199.039794921875,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0v",
			"roundness": null,
			"seed": 1235672766,
			"version": 60,
			"versionNonce": 194972926,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Program\\nGenerates\nVirtual Address",
			"rawText": "Program\\nGenerates Virtual Address",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "1y9ilH22jwQTnqlIlN-Bw",
			"originalText": "Program\\nGenerates Virtual Address",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "flZBl-bB0v3jzit434o3O",
			"type": "rectangle",
			"x": 2060.261576761025,
			"y": -474.6474814235886,
			"width": 260,
			"height": 90,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0w",
			"roundness": null,
			"seed": 18372350,
			"version": 61,
			"versionNonce": 678477118,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "MxlNYKOa"
				},
				{
					"id": "f6seSFs1MewwAAOy7jbQZ",
					"type": "arrow"
				},
				{
					"id": "YEUIt3762z_g2LqBOF5OG",
					"type": "arrow"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"hasTextLink": false
		},
		{
			"id": "MxlNYKOa",
			"type": "text",
			"x": 2065.7416793000875,
			"y": -454.6474814235886,
			"width": 249.039794921875,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0x",
			"roundness": null,
			"seed": 1949353790,
			"version": 60,
			"versionNonce": 1478649214,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Virtual Address\\nVPN  ⟶\nPage Offset",
			"rawText": "Virtual Address\\nVPN  ⟶  Page Offset",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "flZBl-bB0v3jzit434o3O",
			"originalText": "Virtual Address\\nVPN  ⟶  Page Offset",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "f6seSFs1MewwAAOy7jbQZ",
			"type": "arrow",
			"x": 2190.262014261025,
			"y": -609.1474814235886,
			"width": 0,
			"height": 130,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0y",
			"roundness": {
				"type": 2
			},
			"seed": 55652222,
			"version": 61,
			"versionNonce": 737478078,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "mpKsy89U"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					39.5
				],
				[
					0,
					79.5
				],
				[
					0,
					130
				]
			],
			"startBinding": {
				"elementId": "1y9ilH22jwQTnqlIlN-Bw",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "flZBl-bB0v3jzit434o3O",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.044444444444444446
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "mpKsy89U",
			"type": "text",
			"x": 2130.6020640046772,
			"y": -562.1482095616326,
			"width": 119.31990051269531,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [
				"uoCPcZEGZ-gFclVpZFdKn"
			],
			"frameId": null,
			"index": "b0z",
			"roundness": null,
			"seed": 1522175934,
			"version": 61,
			"versionNonce": 1695129086,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"text": "Generate VA",
			"rawText": "Generate VA",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "f6seSFs1MewwAAOy7jbQZ",
			"originalText": "Generate VA",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "YEUIt3762z_g2LqBOF5OG",
			"type": "arrow",
			"x": 2190.762014261025,
			"y": -384.1474814235886,
			"width": 1421.681,
			"height": 345.447,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b10",
			"roundness": {
				"type": 2
			},
			"seed": 1989011454,
			"version": 61,
			"versionNonce": 2019634750,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "bp3PhUEN"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.5,
					54.5
				],
				[
					1167.101,
					139.5
				],
				[
					1167.101,
					224.5
				],
				[
					1421.181,
					345.447
				]
			],
			"startBinding": {
				"elementId": "flZBl-bB0v3jzit434o3O",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "7duO4d1oxkCrNRIra6lOK",
				"mode": "orbit",
				"fixedPoint": [
					0.1473120689655169,
					0.3325758620689655
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "bp3PhUEN",
			"type": "text",
			"x": 1186.9057744742408,
			"y": -216.0146689235886,
			"width": 130.29989624023438,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b11",
			"roundness": null,
			"seed": 650917950,
			"version": 7,
			"versionNonce": 185516962,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997717,
			"link": null,
			"locked": false,
			"text": "Feed to MMU",
			"rawText": "Feed to MMU",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "YEUIt3762z_g2LqBOF5OG",
			"originalText": "Feed to MMU",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "cfaOjAPG5t85gVL_AhXlU",
			"type": "arrow",
			"x": 3463.9170142610246,
			"y": 696.7985185764113,
			"width": 270.1099999999999,
			"height": 450.08900000000017,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b12",
			"roundness": {
				"type": 2
			},
			"seed": 324436094,
			"version": 61,
			"versionNonce": 316454526,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "LjyEOggx"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					15.820999999999913,
					68.55400000000009
				],
				[
					15.820999999999913,
					108.55400000000009
				],
				[
					15.820999999999913,
					223.5540000000001
				],
				[
					15.820999999999913,
					353.5540000000001
				],
				[
					-254.289,
					450.08900000000017
				]
			],
			"startBinding": {
				"elementId": "NqWYoFzj1pCtsBG_3abhd",
				"mode": "orbit",
				"fixedPoint": [
					0.5892308593750002,
					0.9092062499999998
				]
			},
			"endBinding": {
				"elementId": "cZpYrGFOK3vINQ59E6ZBm",
				"mode": "orbit",
				"fixedPoint": [
					1.0144802884615387,
					0.2715952380952385
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "LjyEOggx",
			"type": "text",
			"x": 1301.2507909537328,
			"y": 891.4818435625342,
			"width": 145.35986328125,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b13",
			"roundness": null,
			"seed": 1072545982,
			"version": 6,
			"versionNonce": 1501053538,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Access Allowed",
			"rawText": "Access Allowed",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "cfaOjAPG5t85gVL_AhXlU",
			"originalText": "Access Allowed",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "DXhtp7gIOgWS2lFsNXZIV",
			"type": "arrow",
			"x": 3401.2830142610246,
			"y": 692.2725185764115,
			"width": 734.4090000000001,
			"height": 209.452,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b14",
			"roundness": {
				"type": 2
			},
			"seed": 271775998,
			"version": 61,
			"versionNonce": 490550974,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "Qhya3V9Y"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-19.045000000000073,
					73.07999999999993
				],
				[
					-19.045000000000073,
					113.07999999999993
				],
				[
					-734.4090000000001,
					209.452
				]
			],
			"startBinding": {
				"elementId": "NqWYoFzj1pCtsBG_3abhd",
				"mode": "orbit",
				"fixedPoint": [
					0.39349960937500017,
					0.8950625000000002
				]
			},
			"endBinding": {
				"elementId": "9NR4Nz4sG7U7dv4tslz6G",
				"mode": "orbit",
				"fixedPoint": [
					1.0152461538461537,
					0.3992888888888893
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "Qhya3V9Y",
			"type": "text",
			"x": 1247.3339138159283,
			"y": 816.0118275946562,
			"width": 160.67984008789062,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b15",
			"roundness": null,
			"seed": 1269246270,
			"version": 7,
			"versionNonce": 1165623138,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997717,
			"link": null,
			"locked": false,
			"text": "Violation / Fault",
			"rawText": "Violation / Fault",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "DXhtp7gIOgWS2lFsNXZIV",
			"originalText": "Violation / Fault",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "hGEmxQmNtlgZHsc6tQnE8",
			"type": "arrow",
			"x": 3527.074014261025,
			"y": 1173.0195185764114,
			"width": 860.5809999999999,
			"height": 185.82999999999993,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b16",
			"roundness": {
				"type": 2
			},
			"seed": 564875646,
			"version": 61,
			"versionNonce": 1076080382,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "4bGx2Wzr"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-732.883,
					-122.66699999999992
				],
				[
					-860.5809999999999,
					-185.82999999999993
				]
			],
			"startBinding": {
				"elementId": "2LaQP3JdiOX__79H6zTUj",
				"mode": "orbit",
				"fixedPoint": [
					-2.4038461543358817e-7,
					0.39603333333333296
				]
			},
			"endBinding": {
				"elementId": "9NR4Nz4sG7U7dv4tslz6G",
				"mode": "orbit",
				"fixedPoint": [
					1.0137807692307697,
					0.8685388888888889
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "4bGx2Wzr",
			"type": "text",
			"x": 604.0838034659398,
			"y": 1078.9853310764115,
			"width": 168.59983825683594,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b17",
			"roundness": null,
			"seed": 763392446,
			"version": 6,
			"versionNonce": 1714459106,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Page not present",
			"rawText": "Page not present",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "hGEmxQmNtlgZHsc6tQnE8",
			"originalText": "Page not present",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "_3QWh3S4m3Odn59ZR1ow1",
			"type": "arrow",
			"x": 3074.8630142610245,
			"y": 1300.8525185764115,
			"width": 1270.71,
			"height": 180.89499999999998,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b18",
			"roundness": {
				"type": 2
			},
			"seed": 2051508734,
			"version": 61,
			"versionNonce": 1745952574,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "uvxC4nDv"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.5,
					24.5
				],
				[
					0.5,
					64.5
				],
				[
					0.5,
					104.5
				],
				[
					-1270.21,
					180.89499999999998
				]
			],
			"startBinding": {
				"elementId": "cZpYrGFOK3vINQ59E6ZBm",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "jyIvod1ZI50nIjtrMFEqj",
				"mode": "orbit",
				"fixedPoint": [
					1.0153576923076921,
					0.43245833333333317
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "uvxC4nDv",
			"type": "text",
			"x": 916.4657720328344,
			"y": 1393.9853310764115,
			"width": 106.17990112304688,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b19",
			"roundness": null,
			"seed": 117545534,
			"version": 6,
			"versionNonce": 1875379618,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Cache Miss",
			"rawText": "Cache Miss",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "_3QWh3S4m3Odn59ZR1ow1",
			"originalText": "Cache Miss",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "YyMC3wfODR1j8yZ87SAz7",
			"type": "arrow",
			"x": 3527.074014261025,
			"y": 1299.8525185764115,
			"width": 860.1800000000001,
			"height": 178.17000000000007,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1A",
			"roundness": {
				"type": 2
			},
			"seed": 1250927230,
			"version": 61,
			"versionNonce": 1231456126,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "m7lig4Ic"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-32,
					25.5
				],
				[
					-32,
					65.5
				],
				[
					-32,
					105.5
				],
				[
					-860.1800000000001,
					178.17000000000007
				]
			],
			"startBinding": {
				"elementId": "2LaQP3JdiOX__79H6zTUj",
				"mode": "orbit",
				"fixedPoint": [
					-2.4038461543358817e-7,
					0.9952380952380953
				]
			},
			"endBinding": {
				"elementId": "RHl0PZnY70DizqJQR5Cjf",
				"mode": "orbit",
				"fixedPoint": [
					1.0153230769230766,
					0.36855555555555636
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "m7lig4Ic",
			"type": "text",
			"x": 1295.8568181143773,
			"y": 1393.9853310764115,
			"width": 186.81980895996094,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1B",
			"roundness": null,
			"seed": 934453950,
			"version": 6,
			"versionNonce": 96141666,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Reads entries from",
			"rawText": "Reads entries from",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "YyMC3wfODR1j8yZ87SAz7",
			"originalText": "Reads entries from",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Lfrw2J4x6XCBVREpBbC7c",
			"type": "arrow",
			"x": 2532.410014261025,
			"y": 1010.8525185764115,
			"width": 0,
			"height": 430,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1C",
			"roundness": {
				"type": 2
			},
			"seed": 1091279614,
			"version": 61,
			"versionNonce": 1684334526,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "24Ot98aN"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					39.5
				],
				[
					0,
					184.5
				],
				[
					0,
					314.5
				],
				[
					0,
					354.5
				],
				[
					0,
					394.5
				],
				[
					0,
					430
				]
			],
			"startBinding": {
				"elementId": "9NR4Nz4sG7U7dv4tslz6G",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "RHl0PZnY70DizqJQR5Cjf",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.044444444444444446
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "24Ot98aN",
			"type": "text",
			"x": 385.79276318273673,
			"y": 1353.9853310764115,
			"width": 81.61991882324219,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1D",
			"roundness": null,
			"seed": 1562086206,
			"version": 6,
			"versionNonce": 593003810,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Updates",
			"rawText": "Updates",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Lfrw2J4x6XCBVREpBbC7c",
			"originalText": "Updates",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "VCe3msz8KTd3rO6CN5AVX",
			"type": "arrow",
			"x": 2597.159014261025,
			"y": 1010.8525185764115,
			"width": 925.954,
			"height": 165.18399999999997,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1E",
			"roundness": {
				"type": 2
			},
			"seed": 172192638,
			"version": 61,
			"versionNonce": 153960446,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "2TAwV0rr"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					28.05600000000004,
					39.5
				],
				[
					925.954,
					165.18399999999997
				]
			],
			"startBinding": {
				"elementId": "9NR4Nz4sG7U7dv4tslz6G",
				"mode": "orbit",
				"fixedPoint": [
					0.7471115384615385,
					1
				]
			},
			"endBinding": {
				"elementId": "2LaQP3JdiOX__79H6zTUj",
				"mode": "orbit",
				"fixedPoint": [
					-0.015234855769230867,
					0.4103999999999999
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "2TAwV0rr",
			"type": "text",
			"x": 436.69779980383055,
			"y": 1078.9853310764115,
			"width": 165.4198455810547,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1F",
			"roundness": null,
			"seed": 1388598206,
			"version": 6,
			"versionNonce": 34574562,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Resume after fix",
			"rawText": "Resume after fix",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "VCe3msz8KTd3rO6CN5AVX",
			"originalText": "Resume after fix",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		},
		{
			"id": "Sb7r5vxy33IBULDMmRgxC",
			"type": "arrow",
			"x": 1670.1600142610248,
			"y": 1285.8525185764115,
			"width": 0,
			"height": 140,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1G",
			"roundness": {
				"type": 2
			},
			"seed": 1354705918,
			"version": 61,
			"versionNonce": 1539346494,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "OYclLWCk"
				}
			],
			"updated": 1772788000520,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0,
					39.5
				],
				[
					0,
					79.5
				],
				[
					0,
					119.5
				],
				[
					0,
					140
				]
			],
			"startBinding": {
				"elementId": "zC0eeUGXrIfPIn3iGtnfw",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "jyIvod1ZI50nIjtrMFEqj",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					-0.03333333333333333
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false,
			"hasTextLink": false
		},
		{
			"id": "OYclLWCk",
			"type": "text",
			"x": -511.30720477380623,
			"y": 1393.9853310764115,
			"width": 151.31985473632812,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b1H",
			"roundness": null,
			"seed": 842333246,
			"version": 6,
			"versionNonce": 1433347234,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1772787997715,
			"link": null,
			"locked": false,
			"text": "Translated SPA",
			"rawText": "Translated SPA",
			"fontSize": 20,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Sb7r5vxy33IBULDMmRgxC",
			"originalText": "Translated SPA",
			"autoResize": true,
			"lineHeight": 1.25,
			"hasTextLink": false
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 5,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemArrowType": "round",
		"currentItemFrameRole": null,
		"scrollX": 2248.4005874636405,
		"scrollY": 2380.923007980365,
		"zoom": {
			"value": 0.15
		},
		"currentItemRoundness": "round",
		"gridSize": 20,
		"gridStep": 5,
		"gridModeEnabled": false,
		"gridColor": {
			"Bold": "rgba(217, 217, 217, 0.5)",
			"Regular": "rgba(230, 230, 230, 0.5)"
		},
		"currentStrokeOptions": null,
		"frameRendering": {
			"enabled": true,
			"clip": true,
			"name": true,
			"outline": true,
			"markerName": true,
			"markerEnabled": true
		},
		"objectsSnapModeEnabled": false,
		"activeTool": {
			"type": "selection",
			"customType": null,
			"locked": false,
			"fromSelection": false,
			"lastActiveTool": null
		},
		"disableContextMenu": false
	},
	"files": {}
}
```
%%