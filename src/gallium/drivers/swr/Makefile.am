# Copyright (C) 2015 Intel Corporation.   All Rights Reserved.
#
# Permission is hereby granted, free of charge, to any person obtaining a
# copy of this software and associated documentation files (the "Software"),
# to deal in the Software without restriction, including without limitation
# the rights to use, copy, modify, merge, publish, distribute, sublicense,
# and/or sell copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice (including the next
# paragraph) shall be included in all copies or substantial portions of the
# Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.  IN NO EVENT SHALL
# THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS
# IN THE SOFTWARE.

include Makefile.sources
include $(top_srcdir)/src/gallium/Automake.inc

AM_CXXFLAGS = $(GALLIUM_DRIVER_CFLAGS) $(SWR_CXX11_CXXFLAGS)

noinst_LTLIBRARIES = libmesaswr.la

# gen_knobs.* included here to provide driver access to swr configuration
libmesaswr_la_SOURCES = \
	$(CXX_SOURCES) \
	$(COMMON_CXX_SOURCES) \
	$(JITTER_CXX_SOURCES) \
	rasterizer/codegen/gen_knobs.cpp \
	rasterizer/codegen/gen_knobs.h \
	$(LOADER_SOURCES)

COMMON_CXXFLAGS = \
	-fno-strict-aliasing \
	$(GALLIUM_DRIVER_CFLAGS) \
	$(LLVM_CXXFLAGS) \
	$(SWR_CXX11_CXXFLAGS) \
	-I$(builddir)/rasterizer/codegen \
	-I$(builddir)/rasterizer/core \
	-I$(builddir)/rasterizer/jitter \
	-I$(builddir)/rasterizer/archrast \
	-I$(srcdir)/rasterizer \
	-I$(srcdir)/rasterizer/core \
	-I$(srcdir)/rasterizer/codegen \
	-I$(srcdir)/rasterizer/jitter \
	-I$(srcdir)/rasterizer/archrast

# SWR_AVX_CXXFLAGS needed for intrinsic usage in swr api headers
libmesaswr_la_CXXFLAGS = \
	$(SWR_AVX_CXXFLAGS) \
	$(COMMON_CXXFLAGS)

if HAVE_SWR_AVX
libmesaswr_la_CXXFLAGS += -DHAVE_SWR_AVX
endif

if HAVE_SWR_AVX2
libmesaswr_la_CXXFLAGS += -DHAVE_SWR_AVX2
endif

if HAVE_SWR_KNL
libmesaswr_la_CXXFLAGS += -DHAVE_SWR_KNL
endif

if HAVE_SWR_SKX
libmesaswr_la_CXXFLAGS += -DHAVE_SWR_SKX
endif

COMMON_SOURCES = \
	$(ARCHRAST_CXX_SOURCES) \
	$(COMMON_CXX_SOURCES) \
	$(CORE_CXX_SOURCES) \
	$(MEMORY_CXX_SOURCES) \
	$(BUILT_SOURCES)

BUILT_SOURCES = \
	gen_swr_context_llvm.h \
	rasterizer/codegen/gen_knobs.cpp \
	rasterizer/codegen/gen_knobs.h \
	rasterizer/jitter/gen_state_llvm.h \
	rasterizer/jitter/gen_builder.hpp \
	rasterizer/jitter/gen_builder_x86.hpp \
	rasterizer/archrast/gen_ar_event.hpp \
	rasterizer/archrast/gen_ar_event.cpp \
	rasterizer/archrast/gen_ar_eventhandler.hpp \
	rasterizer/archrast/gen_ar_eventhandlerfile.hpp \
	rasterizer/core/backends/gen_BackendPixelRate0.cpp \
	rasterizer/core/backends/gen_BackendPixelRate1.cpp \
	rasterizer/core/backends/gen_BackendPixelRate2.cpp \
	rasterizer/core/backends/gen_BackendPixelRate3.cpp \
	rasterizer/core/backends/gen_BackendPixelRate.hpp \
	rasterizer/core/backends/gen_rasterizer0.cpp \
	rasterizer/core/backends/gen_rasterizer1.cpp \
	rasterizer/core/backends/gen_rasterizer2.cpp \
	rasterizer/core/backends/gen_rasterizer3.cpp \
	rasterizer/core/backends/gen_rasterizer.hpp

MKDIR_GEN = $(AM_V_at)$(MKDIR_P) $(@D)
PYTHON_GEN = $(AM_V_GEN)$(PYTHON2) $(PYTHON_FLAGS)
gen_swr_context_llvm.h: rasterizer/codegen/gen_llvm_types.py rasterizer/codegen/templates/gen_llvm.hpp swr_context.h
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_llvm_types.py \
		--input $(srcdir)/swr_context.h \
		--output ./gen_swr_context_llvm.h

rasterizer/codegen/gen_knobs.cpp: rasterizer/codegen/gen_knobs.py rasterizer/codegen/knob_defs.py rasterizer/codegen/templates/gen_knobs.cpp rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_knobs.py \
		--output rasterizer/codegen/gen_knobs.cpp \
		--gen_cpp

rasterizer/codegen/gen_knobs.h: rasterizer/codegen/gen_knobs.py rasterizer/codegen/knob_defs.py rasterizer/codegen/templates/gen_knobs.cpp rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_knobs.py \
		--output rasterizer/codegen/gen_knobs.h \
		--gen_h

rasterizer/jitter/gen_state_llvm.h: rasterizer/codegen/gen_llvm_types.py rasterizer/codegen/templates/gen_llvm.hpp rasterizer/core/state.h rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_llvm_types.py \
		--input $(srcdir)/rasterizer/core/state.h \
		--output rasterizer/jitter/gen_state_llvm.h

rasterizer/jitter/gen_builder.hpp: rasterizer/codegen/gen_llvm_ir_macros.py rasterizer/codegen/templates/gen_builder.hpp rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_llvm_ir_macros.py \
		--input $(LLVM_INCLUDEDIR)/llvm/IR/IRBuilder.h \
		--output rasterizer/jitter \
		--gen_h

rasterizer/jitter/gen_builder_x86.hpp: rasterizer/codegen/gen_llvm_ir_macros.py rasterizer/codegen/templates/gen_builder.hpp rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_llvm_ir_macros.py \
		--output rasterizer/jitter \
		--gen_x86_h

rasterizer/archrast/gen_ar_event.hpp: rasterizer/codegen/gen_archrast.py rasterizer/codegen/templates/gen_ar_event.hpp rasterizer/archrast/events.proto rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_archrast.py \
		--proto $(srcdir)/rasterizer/archrast/events.proto \
		--output rasterizer/archrast/gen_ar_event.hpp \
		--gen_event_h

rasterizer/archrast/gen_ar_event.cpp: rasterizer/codegen/gen_archrast.py rasterizer/codegen/templates/gen_ar_event.cpp rasterizer/archrast/events.proto rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_archrast.py \
		--proto $(srcdir)/rasterizer/archrast/events.proto \
		--output rasterizer/archrast/gen_ar_event.cpp \
		--gen_event_cpp

rasterizer/archrast/gen_ar_eventhandler.hpp: rasterizer/codegen/gen_archrast.py rasterizer/codegen/templates/gen_ar_eventhandler.hpp rasterizer/archrast/events.proto rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_archrast.py \
		--proto $(srcdir)/rasterizer/archrast/events.proto \
		--output rasterizer/archrast/gen_ar_eventhandler.hpp \
		--gen_eventhandler_h

rasterizer/archrast/gen_ar_eventhandlerfile.hpp: rasterizer/codegen/gen_archrast.py rasterizer/codegen/templates/gen_ar_eventhandlerfile.hpp rasterizer/archrast/events.proto rasterizer/codegen/gen_common.py
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_archrast.py \
		--proto $(srcdir)/rasterizer/archrast/events.proto \
		--output rasterizer/archrast/gen_ar_eventhandlerfile.hpp \
		--gen_eventhandlerfile_h

rasterizer/core/backends/gen_BackendPixelRate0.cpp \
rasterizer/core/backends/gen_BackendPixelRate1.cpp \
rasterizer/core/backends/gen_BackendPixelRate2.cpp \
rasterizer/core/backends/gen_BackendPixelRate3.cpp \
rasterizer/core/backends/gen_BackendPixelRate.hpp: \
backend.intermediate

# 5 SWR_MULTISAMPLE_TYPE_COUNT
# 2 SWR_MSAA_SAMPLE_PATTERN_COUNT
# 3 SWR_INPUT_COVERAGE_COUNT
# 2 centroid
# 2 forcedSampleCount
# 2 canEarlyZ

# use intermediate rule to tell make that all files can be
# generated in one invocation of gen_backends.py (prevents
# parallel make race condition)
.INTERMEDIATE: backend.intermediate
backend.intermediate: rasterizer/codegen/gen_backends.py rasterizer/codegen/templates/gen_backend.cpp rasterizer/codegen/templates/gen_header_init.hpp
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_backends.py \
		--outdir rasterizer/core/backends \
		--dim 5 2 3 2 2 2 \
		--numfiles 4 \
		--cpp \
		--hpp

rasterizer/core/backends/gen_rasterizer0.cpp \
rasterizer/core/backends/gen_rasterizer1.cpp \
rasterizer/core/backends/gen_rasterizer2.cpp \
rasterizer/core/backends/gen_rasterizer3.cpp \
rasterizer/core/backends/gen_rasterizer.hpp: \
rasterizer.intermediate

# 5 SWR_MULTISAMPLE_TYPE_COUNT
# 2 CenterPattern
# 2 Conservative
# 3 SWR_INPUT_COVERAGE_COUNT
# 5 STATE_VALID_TRI_EDGE_COUNT
# 2 RasterScissorEdges

# use intermediate rule to tell make that all files can be
# generated in one invocation of gen_backends.py (prevents
# parallel make race condition)
.INTERMEDIATE: rasterizer.intermediate
rasterizer.intermediate: rasterizer/codegen/gen_backends.py rasterizer/codegen/templates/gen_rasterizer.cpp rasterizer/codegen/templates/gen_header_init.hpp
	$(MKDIR_GEN)
	$(PYTHON_GEN) \
		$(srcdir)/rasterizer/codegen/gen_backends.py \
		--outdir rasterizer/core/backends \
		--rast \
		--dim 5 2 2 3 5 2 \
		--numfiles 4 \
		--cpp \
		--hpp

COMMON_LDFLAGS = \
	-shared \
	-module \
	-no-undefined \
	$(GC_SECTIONS) \
	$(NO_UNDEFINED)

lib_LTLIBRARIES =

if HAVE_SWR_AVX
lib_LTLIBRARIES += libswrAVX.la

libswrAVX_la_CXXFLAGS = \
	$(SWR_AVX_CXXFLAGS) \
	-DKNOB_ARCH=KNOB_ARCH_AVX \
	$(COMMON_CXXFLAGS)

libswrAVX_la_SOURCES = \
	$(COMMON_SOURCES)

libswrAVX_la_LDFLAGS = \
	$(COMMON_LDFLAGS)
endif

if HAVE_SWR_AVX2
lib_LTLIBRARIES += libswrAVX2.la
libswrAVX2_la_CXXFLAGS = \
	$(SWR_AVX2_CXXFLAGS) \
	-DKNOB_ARCH=KNOB_ARCH_AVX2 \
	$(COMMON_CXXFLAGS)

libswrAVX2_la_SOURCES = \
	$(COMMON_SOURCES)

libswrAVX2_la_LDFLAGS = \
	$(COMMON_LDFLAGS)
endif

if HAVE_SWR_KNL
lib_LTLIBRARIES += libswrKNL.la

libswrKNL_la_CXXFLAGS = \
	$(SWR_KNL_CXXFLAGS) \
	-DKNOB_ARCH=KNOB_ARCH_AVX512 -DAVX512F_STRICT \
	$(COMMON_CXXFLAGS)

libswrKNL_la_SOURCES = \
	$(COMMON_SOURCES)

libswrKNL_la_LDFLAGS = \
	$(COMMON_LDFLAGS)
endif

if HAVE_SWR_SKX
lib_LTLIBRARIES += libswrSKX.la

libswrSKX_la_CXXFLAGS = \
	$(SWR_SKX_CXXFLAGS) \
	-DKNOB_ARCH=KNOB_ARCH_AVX512 \
	$(COMMON_CXXFLAGS)

libswrSKX_la_SOURCES = \
	$(COMMON_SOURCES)

libswrSKX_la_LDFLAGS = \
	$(COMMON_LDFLAGS)
endif

include $(top_srcdir)/install-gallium-links.mk

# Generated gen_builder.hpp is not backwards compatible. So ship only one
# created with the oldest supported version of LLVM.
dist-hook:
if SWR_INVALID_LLVM_VERSION
	@echo "*******************************************************"
	@echo "LLVM 3.9.0 or LLVM 3.9.1 required to create the tarball"
	@echo "*******************************************************"
	@test
endif

EXTRA_DIST = \
	SConscript \
	rasterizer/archrast/events.proto \
	rasterizer/codegen/gen_llvm_ir_macros.py \
	rasterizer/codegen/gen_llvm_types.py \
	rasterizer/codegen/gen_archrast.py \
	rasterizer/codegen/gen_backends.py \
	rasterizer/codegen/gen_common.py \
	rasterizer/codegen/gen_knobs.py \
	rasterizer/codegen/knob_defs.py \
	rasterizer/codegen/templates/gen_ar_event.cpp \
	rasterizer/codegen/templates/gen_ar_event.hpp \
	rasterizer/codegen/templates/gen_ar_eventhandler.hpp \
	rasterizer/codegen/templates/gen_ar_eventhandlerfile.hpp \
	rasterizer/codegen/templates/gen_backend.cpp \
	rasterizer/codegen/templates/gen_builder.hpp \
	rasterizer/codegen/templates/gen_header_init.hpp \
	rasterizer/codegen/templates/gen_knobs.cpp \
	rasterizer/codegen/templates/gen_llvm.hpp \
	rasterizer/codegen/templates/gen_rasterizer.cpp
