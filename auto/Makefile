## Copyright (C) 2002-2008, Marcelo E. Magallon <mmagallo[]debian org>
## Copyright (C) 2002-2008, Milan Ikits <milan ikits[]ieee org>
##
## This program is distributed under the terms and conditions of the GNU
## General Public License Version 2 as published by the Free Software
## Foundation or, at your option, any later version.

include ../config/version

#GLEW_SPLIT_SOURCE = yes

SHELL = bash
REGISTRY = registry
REGISTRY_URL = http://www.opengl.org/registry/
#http://oss.sgi.com/projects/ogl-sample/registry/
REGISTRY_SPECIFIC_PATH = /registry/
BIN = bin
SRC = src
CORE = core
CORE_ES_WITH_OPENGL = core/es
CORE_ES = core_es
CORE_EGL = core_egl
COMMON = common
EXT = extensions
BLACKLIST = blacklist

################################################################################
# OpenGL ES support 
REGISTRY_ES = registry_es
REGISTRY_ES_URL = http://www.khronos.org/registry/gles/
REGISTRY_ES_SPECIFIC_PATH = /registry/gles/
EXT_ES = extensions_es

REGISTRY_EGL = registry_egl
REGISTRY_EGL_URL = http://www.khronos.org/registry/egl/
REGISTRY_EGL_SPECIFIC_PATH = /registry/egl/
EXT_EGL = extensions_egl
################################################################################
GL_CORE_SPEC := $(CORE)/GL_VERSION*
GLX_CORE_SPEC := $(CORE)/GLX_VERSION*
GLES_CORE_SPEC := $(CORE_ES)/GL_ES_VERSION*
EGL_CORE_SPEC := $(CORE_EGL)/EGL_VERSION*
GLES_CORE_WITH_OPENGL_SPEC := $(CORE_ES_WITH_OPENGL)/GL_ES_VERSION*
ifeq (custom,$(MAKECMDGOALS))
#GL_CORE_SPEC := $(shell grep GL_VERSION custom.txt | perl -e 's/\(.*\)/$(CORE)\/\1/g;')
GL_EXT_SPEC := $(shell grep "^[ \t]*GL_" custom.txt | grep -v GL_VERSION | perl -e 's/\(.*\)/$(EXT)\/\1/g;')
WGL_EXT_SPEC := $(shell grep "^[ \t]*WGL_" custom.txt | perl -e 's/\(.*\)/$(EXT)\/\1/g;')
#GLX_CORE_SPEC := $(shell grep GLX_VERSION custom.txt | perl -e 's/\(.*\)/$(CORE)\/\1/g;')
GLX_EXT_SPEC := $(shell grep "^[ \t]*GLX_" custom.txt | grep -v GLX_VERSION | perl -e 's/\(.*\)/$(EXT)\/\1/g;')
else
GL_EXT_SPEC := $(EXT)/GL_*
WGL_EXT_SPEC := $(EXT)/WGL_*
GLX_EXT_SPEC := $(EXT)/GLX_*
GLES_EXT_SPEC := $(EXT_ES)/GL_*
GLES_COMMON_EXT_SPEC := $(COMMON)/GL_*
EGL_EXT_SPEC := $(EXT_EGL)/EGL_*
endif

PARSE_SPEC = parse_spec.pl
SYSTEM = $(strip $(shell uname -s))

TOP = ..
I.DEST = $(TOP)/include/GL
S.DEST = $(TOP)/src
D.DEST = $(TOP)/doc
B.DEST = $(TOP)/build/vc6

I.TARGETS = \
	$(I.DEST)/glew.h \
	$(I.DEST)/wglew.h \
	$(I.DEST)/glxew.h \
	$(I.DEST)/glesew.h \
	$(I.DEST)/eglew.h 
	

ifeq (yes,$(GLEW_SPLIT_SOURCE))
S.TARGETS = \
	$(S.DEST)/glew_def.c \
	$(S.DEST)/glew_init.c \
	$(S.DEST)/glew_str.c \
	$(S.DEST)/glewinfo.c
else
S.TARGETS = \
	$(S.DEST)/glew.c \
	$(S.DEST)/glewinfo.c
endif

D.TARGETS = \
	$(D.DEST)/index.html \
	$(D.DEST)/install.html \
	$(D.DEST)/basic.html \
	$(D.DEST)/advanced.html \
	$(D.DEST)/build.html \
	$(D.DEST)/credits.html \
	$(D.DEST)/log.html \
	$(D.DEST)/glew.html \
	$(D.DEST)/glesew.html	\
	$(D.DEST)/wglew.html \
	$(D.DEST)/glxew.html	\
	$(D.DEST)/eglew.html

B.TARGETS = \
	$(B.DEST)/glew.rc \
	$(B.DEST)/glewinfo.rc \
	$(B.DEST)/visualinfo.rc

all custom: $(I.TARGETS) $(S.TARGETS) $(D.TARGETS) $(B.TARGETS)

registry: $(REGISTRY)/.dummy
ext: $(EXT)/.dummy

$(REGISTRY)/.dummy: $(BIN)/update_registry.sh
	@echo "--------------------------------------------------------------------"
	@echo "Downloading registry"
	@echo "--------------------------------------------------------------------"
	$(BIN)/update_registry.sh $(REGISTRY) $(REGISTRY_URL) $(REGISTRY_SPECIFIC_PATH)
	touch $@
$(REGISTRY_ES)/.dummy : $(BIN)/update_registry.sh
	@echo "--------------------------------------------------------------------"
	@echo "Downloading registry - ES"
	@echo "--------------------------------------------------------------------"
	$(BIN)/update_registry.sh $(REGISTRY_ES) $(REGISTRY_ES_URL) $(REGISTRY_ES_SPECIFIC_PATH)
	touch $@
$(REGISTRY_EGL)/.dummy : $(BIN)/update_registry.sh
	@echo "--------------------------------------------------------------------"
	@echo "Downloading registry - EGL"
	@echo "--------------------------------------------------------------------"
	$(BIN)/update_registry.sh $(REGISTRY_EGL) $(REGISTRY_EGL_URL) $(REGISTRY_EGL_SPECIFIC_PATH)
	touch $@

$(EXT)/.dummy: $(REGISTRY)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating descriptors"
	@echo "--------------------------------------------------------------------"
	rm -rf $(EXT)
	$(BIN)/update_ext.sh $(EXT) $(REGISTRY) $(BLACKLIST)
ifeq ($(patsubst Darwin%,Darwin,$(SYSTEM)), Darwin)
	find $(CORE) -maxdepth 1 -type f | grep -v VERSION | grep -v "~" | \
	xargs -J % cp % $(EXT)
else
	find $(CORE) -maxdepth 1 -type f | grep -v VERSION | grep -v "~" | \
	xargs cp --target-directory=$(EXT)
endif
	touch $@

$(EXT_ES)/.dummy : $(REGISTRY_ES)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating descriptors - ES"
	@echo "--------------------------------------------------------------------"
	rm -rf $(EXT_ES)
	$(BIN)/update_ext_es.sh $(EXT_ES) $(REGISTRY_ES) $(BLACKLIST)
	touch $@

$(EXT_EGL)/.dummy :$(REGISTRY_EGL)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating descriptors - EGL"
	@echo "--------------------------------------------------------------------"
	rm -rf $(EXT_EGL)
	$(BIN)/update_ext_egl.sh $(EXT_EGL) $(REGISTRY_EGL) $(BLACKLIST)
	touch $@
	
$(I.DEST)/glew.h: $(EXT)/.dummy $(EXT_ES)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glew.h"
	@echo "--------------------------------------------------------------------"
	test -d $(I.DEST) || mkdir -p $(I.DEST)
	#---move the common extensions to common folder---
	$(BIN)/move_common_ext.sh $(EXT) $(EXT_ES) $(COMMON)
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/mesa_license.h >> $@
	cat $(SRC)/khronos_license.h >> $@
	cat $(SRC)/glew_head.h >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL '' $(GL_CORE_SPEC) >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL '' $(GL_EXT_SPEC) >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL 'NO_ES' $(GLES_CORE_WITH_OPENGL_SPEC) >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL 'NO_ES' $(GLES_EXT_SPEC) >> $@
	echo -e "/* ------------------------------------------------------------------------- */\n\n#if defined(GLEW_MX) && defined(_WIN32)\n#define GLEW_FUN_EXPORT\n#else\n#define GLEW_FUN_EXPORT GLEWAPI\n#endif /* GLEW_MX */\n" >> $@
	echo -e "#if defined(GLEW_MX)\n#define GLEW_VAR_EXPORT\n#else\n#define GLEW_VAR_EXPORT GLEWAPI\n#endif /* GLEW_MX */\n" >> $@
	echo -e "#if defined(GLEW_MX) && defined(_WIN32)\nstruct GLEWContextStruct\n{\n#endif /* GLEW_MX */" >> $@
	$(BIN)/make_struct_fun.pl GLEW_FUN_EXPORT $(GL_CORE_SPEC) $(GL_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_NO_ES)\n" >> $@
	$(BIN)/make_struct_fun.pl GLEW_FUN_EXPORT $(GLES_CORE_WITH_OPENGL_SPEC) $(GLES_EXT_SPEC) >> $@
	echo -e "#endif /* !(GLEW_NO_ES) */\n" >> $@
	echo -e "\n#if defined(GLEW_MX) && !defined(_WIN32)\nstruct GLEWContextStruct\n{\n#endif /* GLEW_MX */\n" >> $@
	$(BIN)/make_struct_var.pl GLEW_VAR_EXPORT $(GL_CORE_SPEC) $(GL_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_NO_ES)\n" >> $@
	$(BIN)/make_struct_var.pl GLEW_VAR_EXPORT $(GLES_CORE_WITH_OPENGL_SPEC) $(GLES_EXT_SPEC) >> $@
	echo -e "#endif /* !(GLEW_NO_ES) */\n" >> $@
	echo -e "\n#ifdef GLEW_MX\n}; /* GLEWContextStruct */\n#endif /* GLEW_MX */\n" >> $@
	rm -f $@.bak
	cat $(SRC)/glew_tail.h >> $@
	#---remove the common folder and copy back the files---
	$(BIN)/move_back_common_ext.sh $(EXT_ES) $(COMMON)

$(I.DEST)/glesew.h: $(EXT_ES)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glesew.h"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/glesew_head.h >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL '' $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_header.pl GLAPIENTRY GL '' $(GLES_EXT_SPEC) >> $@
	echo -e "/* ------------------------------------------------------------------------- */\n\n#if defined(GLEW_MX) && defined(_WIN32)\n#define GLEW_FUN_EXPORT\n#else\n#define GLEW_FUN_EXPORT GLEWAPI\n#endif /* GLEW_MX */\n" >> $@
	echo -e "#if defined(GLEW_MX)\n#define GLEW_VAR_EXPORT\n#else\n#define GLEW_VAR_EXPORT GLEWAPI\n#endif /* GLEW_MX */\n" >> $@
	echo -e "#if defined(GLEW_MX) && defined(_WIN32)\nstruct GLEWContextStruct\n{\n#endif /* GLEW_MX */" >> $@
	$(BIN)/make_struct_fun.pl GLEW_FUN_EXPORT $(GLES_CORE_SPEC) $(GLES_EXT_SPEC) >> $@
	echo -e "\n#if defined(GLEW_MX) && !defined(_WIN32)\nstruct GLEWContextStruct\n{\n#endif /* GLEW_MX */\n" >> $@
	$(BIN)/make_struct_var.pl GLEW_VAR_EXPORT $(GLES_CORE_SPEC) $(GLES_EXT_SPEC) >> $@
	echo -e "\n#ifdef GLEW_MX\n}; /* GLEWContextStruct */\n#endif /* GLEW_MX */\n" >> $@
	perl -e 's/GLEW_VAR_EXPORT GLboolean __GLEW_ES_VERSION_1_1;/GLEW_VAR_EXPORT GLboolean __GLEW_ES_VERSION_1_0;\nGLEW_VAR_EXPORT GLboolean __GLEW_ES_VERSION_1_1;/' -pi $@
	rm -f $@.bak
	echo -e "\n#endif /* __glesew_h__ */" >> $@

$(I.DEST)/eglew.h: $(EXT_EGL)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating eglew.h"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/khronos_license.h >> $@
	cat $(SRC)/eglew_head.h >> $@
	$(BIN)/make_header.pl EGLAPIENTRY EGL '' $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_header.pl EGLAPIENTRY EGL '' $(EGL_EXT_SPEC) >> $@
	echo -e "/* ------------------------------------------------------------------------- */\n\n#if defined(GLEW_MX) && defined(_WIN32)\n #define EGLEW_FUN_EXPORT \n #else \n #define EGLEW_FUN_EXPORT GLEWAPI \n #endif /* GLEW_MX */\n" >> $@
	echo -e  "#if defined(GLEW_MX)\n #define EGLEW_VAR_EXPORT\n#else\n#define EGLEW_VAR_EXPORT GLEWAPI \n #endif /* GLEW_MX */\n\n#if defined (GLEW_MX) && defined(_WIN32)\nstruct EGLEWContextStruct\n{\n#endif /* GLEW_MX */" >> $@
	$(BIN)/make_struct_fun.pl EGLEW_FUN_EXPORT $(EGL_CORE_SPEC) $(EGL_EXT_SPEC) >> $@
	echo -e "\n#if defined(GLEW_MX) && !defined(_WIN32)\nstruct EGLEWContextStruct\n{\n#endif /* GLEW_MX */\n" >> $@
	$(BIN)/make_struct_var.pl EGLEW_VAR_EXPORT $(EGL_CORE_SPEC) $(EGL_EXT_SPEC) >> $@
	echo -e "\n#ifdef GLEW_MX\n}; /* EGLEWContextStruct */\n#endif /* GLEW_MX */\n" >> $@
	perl -e 's/GLEW_VAR_EXPORT GLboolean __EGLEW_VERSION_1_2;/GLEW_VAR_EXPORT GLboolean __EGLEW_VERSION_1_1;\nEGLEW_VAR_EXPORT GLboolean __EGLEW_VERSION_1_2;/' -pi $@
	cat $(SRC)/eglew_tail.h >> $@

$(I.DEST)/wglew.h: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating wglew.h"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/khronos_license.h >> $@
	cat $(SRC)/wglew_head.h >> $@
	$(BIN)/make_header.pl WINAPI WGL '' $(WGL_EXT_SPEC) >> $@
	echo -e "/* ------------------------------------------------------------------------- */\n\n#ifdef GLEW_MX\n#define WGLEW_EXPORT\n#else\n#define WGLEW_EXPORT GLEWAPI\n#endif /* GLEW_MX */\n\n#ifdef GLEW_MX\nstruct WGLEWContextStruct\n{\n#endif /* GLEW_MX */" >> $@
	$(BIN)/make_struct_fun.pl WGLEW_EXPORT $(WGL_EXT_SPEC) >> $@
	$(BIN)/make_struct_var.pl WGLEW_EXPORT $(WGL_EXT_SPEC) >> $@
	echo -e "\n#ifdef GLEW_MX\n}; /* WGLEWContextStruct */\n#endif /* GLEW_MX */\n" >> $@
	cat $(SRC)/wglew_tail.h >> $@

$(I.DEST)/glxew.h: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glxew.h"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/mesa_license.h >> $@
	cat $(SRC)/khronos_license.h >> $@
	cat $(SRC)/glxew_head.h >> $@
	$(BIN)/make_header.pl '' GLX '' $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_header.pl '' GLX '' $(GLX_EXT_SPEC) >> $@
	echo -e "/* ------------------------------------------------------------------------- */\n\n#ifdef GLEW_MX\n#define GLXEW_EXPORT\n#else\n#define GLXEW_EXPORT extern\n#endif /* GLEW_MX */" >> $@
	$(BIN)/make_struct_fun.pl extern $(GLX_CORE_SPEC) $(GLX_EXT_SPEC) >> $@
	echo -e "\n#if defined(GLEW_MX)\nstruct GLXEWContextStruct\n{\n#endif /* GLEW_MX */\n" >> $@
	$(BIN)/make_struct_var.pl GLXEW_EXPORT $(GLX_CORE_SPEC) $(GLX_EXT_SPEC) >> $@
	echo -e "\n#ifdef GLEW_MX\n}; /* GLXEWContextStruct */\n#endif /* GLEW_MX */\n" >> $@
	perl -e 's/GLXEW_EXPORT GLboolean __GLXEW_VERSION_1_2;/GLXEW_EXPORT GLboolean __GLXEW_VERSION_1_0;\nGLXEW_EXPORT GLboolean __GLXEW_VERSION_1_1;\nGLXEW_EXPORT GLboolean __GLXEW_VERSION_1_2;/' -pi $@
	cat $(SRC)/glxew_tail.h >> $@

$(S.DEST)/glew.c: $(EXT)/.dummy $(EXT_EGL)/.dummy $(EXT_ES)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glew.c"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/glew_head.c >> $@
	#---move the common extensions to common folder---
	$(BIN)/move_common_ext.sh $(EXT) $(EXT_ES) $(COMMON)
	echo -e "\n#if !defined(_WIN32) || !defined(GLEW_MX)" >> $@
	echo -e "\n#ifndef GLEW_ES_ONLY \n" >> $@
	$(BIN)/make_def_fun.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl GL $(GL_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_NO_ES)\n" >> $@
	$(BIN)/make_def_fun.pl GL $(GLES_CORE_WITH_OPENGL_SPEC) >> $@
	$(BIN)/make_def_fun.pl GL $(GLES_EXT_SPEC) >> $@
	echo -e "\n#endif /* !(GLEW_NO_ES) */\n" >> $@
	echo -e "\n#else   \n" >> $@
	$(BIN)/make_def_fun.pl GL $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl GL $(GLES_EXT_SPEC) >> $@
	$(BIN)/make_def_fun.pl GL $(GLES_COMMON_EXT_SPEC) >> $@
	echo -e "\n#endif /* GLEW_ES_ONLY */  \n" >> $@	
	echo -e "\n#endif /* !WIN32 || !GLEW_MX */" >> $@
	echo -e "\n#if !defined(GLEW_MX)" >> $@;
	echo -e "\n#ifndef GLEW_ES_ONLY \n" >> $@
	$(BIN)/make_def_var.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl GL $(GL_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_NO_ES)\n" >> $@
	$(BIN)/make_def_var.pl GL $(GLES_CORE_WITH_OPENGL_SPEC) >> $@
	$(BIN)/make_def_var.pl GL $(GLES_EXT_SPEC) >> $@
	echo -e "\n#endif /* !(GLEW_NO_ES) */\n" >> $@
	echo -e "\n#else \n" >> $@
	$(BIN)/make_def_var.pl GL $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl GL $(GLES_EXT_SPEC) >> $@
	$(BIN)/make_def_var.pl GL $(GLES_COMMON_EXT_SPEC) >> $@
	echo -e "\n#endif /* GLEW_ES_ONLY */ \n" >> $@
	echo -e "\n#endif /* !GLEW_MX */\n" >> $@;
	$(BIN)/make_init.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_init.pl GL $(GL_EXT_SPEC) >> $@
	$(BIN)/make_init.pl GL $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_init.pl GL $(GLES_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_gl.c >> $@
	$(BIN)/make_list.pl $(GL_CORE_SPEC) | grep -v '\"GL_VERSION' >> $@
	$(BIN)/make_list.pl $(GL_EXT_SPEC) >> $@
	$(BIN)/make_list.pl $(GLES_CORE_SPEC) | grep -v '\"GL_ES_VERSION' >> $@  
	$(BIN)/make_list.pl $(GLES_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}\n" >> $@
	#---------egl------------
	echo -e "\n#if defined (GLEW_USE_LIB_ES)\n" >> $@
	echo -e "\n#if defined (GLEW_INC_EGL) \n" >> $@
	echo -e "\n#if !defined(_WIN32) || !defined(GLEW_MX)" >> $@
	$(BIN)/make_def_fun.pl EGL $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl EGL $(EGL_EXT_SPEC) >> $@
	echo -e "\n#endif /* !WIN32 || !GLEW_MX */" >> $@
	echo -e "\n#if !defined(GLEW_MX)" >> $@;
	echo -e "\nGLboolean __EGLEW_VERSION_1_1 = GL_FALSE;" >> $@
	$(BIN)/make_def_var.pl EGL $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl EGL $(EGL_EXT_SPEC) >> $@
	echo -e "\n#endif /* !GLEW_MX */\n" >> $@;
	$(BIN)/make_init.pl EGL $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_init.pl EGL $(EGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_egl.c >> $@
	$(BIN)/make_list.pl $(EGL_CORE_SPEC) | grep -v '\"EGL_VERSION' >> $@
	$(BIN)/make_list.pl $(EGL_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}" >> $@;
	echo -e "\n#endif /* GLEW_INC_EGL */" >> $@
	echo -e "\n#elif defined(_WIN32)" >> $@
	echo -e "\n#if !defined(GLEW_MX)" >> $@
	$(BIN)/make_def_fun.pl WGL $(WGL_EXT_SPEC) >> $@
	$(BIN)/make_def_var.pl WGL $(WGL_EXT_SPEC) >> $@
	echo -e "\n#endif /* !GLEW_MX */\n" >> $@;
	$(BIN)/make_init.pl WGL $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_wgl.c >> $@
	$(BIN)/make_list.pl $(WGL_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}" >> $@;
	echo -e "\n#elif !defined(__APPLE__) || defined(GLEW_APPLE_GLX)" >> $@
	$(BIN)/make_def_fun.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl GLX $(GLX_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_MX)" >> $@;
	echo -e "\nGLboolean __GLXEW_VERSION_1_0 = GL_FALSE;" >> $@
	echo -e "GLboolean __GLXEW_VERSION_1_1 = GL_FALSE;" >> $@
	$(BIN)/make_def_var.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl GLX $(GLX_EXT_SPEC) >> $@
	echo -e "\n#endif /* !GLEW_MX */\n" >> $@;
	$(BIN)/make_init.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_init.pl GLX $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_glx.c >> $@
	$(BIN)/make_list.pl $(CORE)/GLX_VERSION_1_3 | grep -v '\"GLX_VERSION' >> $@
	$(BIN)/make_list.pl $(GLX_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}" >> $@
	echo -e "\n#endif /* !__APPLE__ || GLEW_APPLE_GLX */\n" >> $@;
	cat $(SRC)/glew_init_tail.c >> $@
	cat $(SRC)/glew_str_head.c >> $@
	$(BIN)/make_str.pl $(GL_CORE_SPEC) $(GL_EXT_SPEC) $(GLES_CORE_SPEC) $(GLES_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_egl.c >> $@
	$(BIN)/make_str.pl $(EGL_CORE_SPEC) $(EGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_wgl.c >> $@
	$(BIN)/make_str.pl $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_glx.c >> $@
	$(BIN)/make_str.pl $(GLX_CORE_SPEC) $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_tail.c >> $@
	#---remove the common folder and copy back the files---
	$(BIN)/move_back_common_ext.sh $(EXT_ES) $(COMMON)
	perl -e "s/GLEW_VERSION_STRING/$(GLEW_VERSION)/g" -pi $@
	perl -e "s/GLEW_VERSION_MAJOR_STRING/$(GLEW_MAJOR)/g" -pi $@
	perl -e "s/GLEW_VERSION_MINOR_STRING/$(GLEW_MINOR)/g" -pi $@
	perl -e "s/GLEW_VERSION_MICRO_STRING/$(GLEW_MICRO)/g" -pi $@
	perl -e "s/GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader\(GLEW_CONTEXT_ARG_VAR_INIT\);/{ GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader(GLEW_CONTEXT_ARG_VAR_INIT); _glewInit_GL_ARB_vertex_program(GLEW_CONTEXT_ARG_VAR_INIT); }/g" -pi $@
	perl -e "s/\(\(glColorSubTable = /((glBlendEquation = (PFNGLBLENDEQUATIONPROC)glewGetProcAddress((const GLubyte*)\"glBlendEquation\")) == NULL) || r;\n  r = ((glColorSubTable = /g" -pi $@
	perl -00 -e "s/#ifdef GL_OES_EGL_image_external\n\n/#ifdef GL_OES_EGL_image_external\n\nstatic GLboolean _glewInit_GL_OES_EGL_image_external(GLEW_CONTEXT_ARG_DEF_INIT)\n{\n\tGLboolean r = GL_FALSE;\n\tr = ((glEGLImageTargetTexture2DOES = (PFNGLEGLIMAGETARGETTEXTURE2DOESPROC)glewGetProcAddress((const GLubyte*)\"glEGLImageTargetTexture2DOES\")) == NULL) || r;\n\treturn r;\n}\n/" -pi $@
	perl -00 -e "s/#ifdef EGL_KHR_fence_sync\n\n/#ifdef EGL_KHR_fence_sync\n\nstatic GLboolean _glewInit_EGL_KHR_fence_sync (EGLEW_CONTEXT_ARG_DEF_INIT)\n{\n\tGLboolean r = GL_FALSE;\n\tr = ((eglClientWaitSyncKHR = (PFNEGLCLIENTWAITSYNCKHRPROC)glewGetProcAddress((const GLubyte*)\"eglClientWaitSyncKHR\")) == NULL) || r;\n\tr = ((eglCreateSyncKHR = (PFNEGLCREATESYNCKHRPROC)glewGetProcAddress((const GLubyte*)\"eglCreateSyncKHR\")) == NULL) || r;\n\tr = ((eglDestroySyncKHR = (PFNEGLDESTROYSYNCKHRPROC)glewGetProcAddress((const GLubyte*)\"eglDestroySyncKHR\")) == NULL) || r;\n\tr = ((eglGetSyncAttribKHR = (PFNEGLGETSYNCATTRIBKHRPROC)glewGetProcAddress((const GLubyte*)\"eglGetSyncAttribKHR\")) == NULL) || r;\n\treturn r;\n}\n/" -pi $@
	perl -e 's/_glewSearchExtension\("GL_OES_EGL_image_external ", extStart, extEnd\);/_glewSearchExtension("GL_OES_EGL_image_external ", extStart, extEnd);\n\tif (glewExperimental || GLEW_OES_EGL_image_external) CONST_CAST(GLEW_OES_EGL_image_external) = !_glewInit_GL_OES_EGL_image_external(GLEW_CONTEXT_ARG_VAR_INIT);/g' -pi $@
	perl -e 's/_glewSearchExtension\("EGL_KHR_fence_sync", extStart, extEnd\);/_glewSearchExtension("EGL_KHR_fence_sync", extStart, extEnd);\n\tif (glewExperimental || EGLEW_KHR_fence_sync) CONST_CAST(EGLEW_KHR_fence_sync) = !_glewInit_EGL_KHR_fence_sync(GLEW_CONTEXT_ARG_VAR_INIT);/g' -pi $@
	rm -f $@.bak

$(S.DEST)/glew_def.c: $(EXT)/.dummy
	cp -f $(SRC)/glew_license.h $@
	echo -e "#include \"glew_utils.h\"\n\n#if !defined(_WIN32) || !defined(GLEW_MX)" >> $@
	$(BIN)/make_def_fun.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl GL $(GL_EXT_SPEC) >> $@
	echo -e "\n#endif /* !WIN32 || !GLEW_MX */" >> $@
	echo -e "\n#if !defined(GLEW_MX)\n\nGLboolean __GLEW_VERSION_1_1 = GL_FALSE;" >> $@
	$(BIN)/make_def_var.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl GL $(GL_EXT_SPEC) >> $@
	echo -e "\n#if defined(_WIN32)" >> $@
	$(BIN)/make_def_fun.pl WGL $(WGL_EXT_SPEC) >> $@
	$(BIN)/make_def_var.pl WGL $(WGL_EXT_SPEC) >> $@
	echo -e "\n#endif /* _WIN32 */" >> $@
	echo -e "\n#endif /* !GLEW_MX */" >> $@;
	echo -e "\n#if !defined(_WIN32) && (!defined(__APPLE__) || defined(GLEW_APPLE_GLX))" >> $@
	$(BIN)/make_def_fun.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_def_fun.pl GLX $(GLX_EXT_SPEC) >> $@
	echo -e "\n#if !defined(GLEW_MX)" >> $@;
	echo -e "\nGLboolean __GLXEW_VERSION_1_0 = GL_FALSE;" >> $@
	echo -e "GLboolean __GLXEW_VERSION_1_1 = GL_FALSE;" >> $@
	$(BIN)/make_def_var.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_def_var.pl GLX $(GLX_EXT_SPEC) >> $@
	echo -e "\n#endif /* !GLEW_MX */" >> $@;
	echo -e "\n#endif /* !__APPLE__ || GLEW_APPLE_GLX */" >> $@;
	rm -f $@.bak

$(S.DEST)/glew_init.c: $(EXT)/.dummy
	cp -f $(SRC)/glew_license.h $@
	echo -e "#include \"glew_utils.h\"\n" >> $@
	$(BIN)/make_init.pl GL $(GL_CORE_SPEC) >> $@
	$(BIN)/make_init.pl GL $(GL_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_gl.c >> $@
	$(BIN)/make_list.pl $(GL_CORE_SPEC) | grep -v '\"GL_VERSION' >> $@
	$(BIN)/make_list.pl $(GL_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}\n\n#if defined(_WIN32)\n" >> $@;
	$(BIN)/make_init.pl WGL $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_wgl.c >> $@
	$(BIN)/make_list.pl $(WGL_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}\n\n" >> $@;
	echo -e "\n#elif !defined(__APPLE__) || defined(GLEW_APPLE_GLX)\n" >> $@
	$(BIN)/make_init.pl GLX $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_init.pl GLX $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/glew_init_glx.c >> $@
	$(BIN)/make_list.pl $(CORE)/GLX_VERSION_1_3 | grep -v '\"GLX_VERSION' >> $@
	$(BIN)/make_list.pl $(GLX_EXT_SPEC) >> $@
	echo -e "\n  return GLEW_OK;\n}\n\n#endif /* !__APPLE__ || GLEW_APPLE_GLX */\n" >> $@;
	cat $(SRC)/glew_init_tail.c >> $@
	perl -e "s/GLEW_VERSION_STRING/$(GLEW_VERSION)/g" -pi $@
	perl -e "s/GLEW_VERSION_MAJOR_STRING/$(GLEW_MAJOR)/g" -pi $@
	perl -e "s/GLEW_VERSION_MINOR_STRING/$(GLEW_MINOR)/g" -pi $@
	perl -e "s/GLEW_VERSION_MICRO_STRING/$(GLEW_MICRO)/g" -pi $@
	perl -e "s/GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader\(GLEW_CONTEXT_ARG_VAR_INIT\);/{ GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader(GLEW_CONTEXT_ARG_VAR_INIT); _glewInit_GL_ARB_vertex_program(GLEW_CONTEXT_ARG_VAR_INIT); }/g" -pi $@
	perl -e "s/\(\(glBlendColor = /((glBlendEquation = (PFNGLBLENDEQUATIONPROC)glewGetProcAddress((const GLubyte*)\"glBlendEquation\")) == NULL) || r;\n  r = ((glBlendColor = /g" -pi $@
	rm -f $@.bak

$(S.DEST)/glew_str.c: $(EXT)/.dummy
	cp -f $(SRC)/glew_license.h $@
	echo -e "\n#include \"glew_utils.h\"\n" >> $@
	cat $(SRC)/glew_str_head.c >> $@
	$(BIN)/make_str.pl $(GL_CORE_SPEC) $(GL_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_wgl.c >> $@
	$(BIN)/make_str.pl $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_glx.c >> $@
	$(BIN)/make_str.pl $(GLX_CORE_SPEC) $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/glew_str_tail.c >> $@
#	perl -e "s/GLEW_VERSION_STRING/$(GLEW_VERSION)/g" -pi $@
#	perl -e "s/GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader\(GLEW_CONTEXT_ARG_VAR_INIT\);/{ GLEW_ARB_vertex_shader = !_glewInit_GL_ARB_vertex_shader(GLEW_CONTEXT_ARG_VAR_INIT); _glewInit_GL_ARB_vertex_program(GLEW_CONTEXT_ARG_VAR_INIT); }/g" -pi $@
	rm -f $@.bak

$(S.DEST)/glewinfo.c: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glewinfo.c"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/glew_license.h $@
	cat $(SRC)/glewinfo_head.c >> $@
	#---move the common extensions to common folder---
	$(BIN)/move_common_ext.sh $(EXT) $(EXT_ES) $(COMMON)
	$(BIN)/make_info.pl $(GL_CORE_SPEC) >> $@
	$(BIN)/make_info.pl $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_info.pl $(GL_EXT_SPEC) >> $@
	$(BIN)/make_info.pl $(GLES_EXT_SPEC) >> $@
	echo -e "#if defined (GLEW_USE_LIB_ES)\n" >> $@
	echo -e "#if defined (GLEW_INC_EGL)\n" >> $@
	echo -e "#ifdef EGL_VERSION_1_1\n\nstatic void _glewInfo_EGL_VERSION_1_1() \n{\n\tglewPrintExt(\"EGL_VERSION_1_1\", EGLEW_VERSION_1_1, EGLEW_VERSION_1_1, EGLEW_VERSION_1_1);\n}\n\n#endif /* EGL_VERSION_1_1 */\n" >> $@
	$(BIN)/make_info.pl $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_info.pl $(EGL_EXT_SPEC) >> $@
	echo -e "#endif /* GLEW_INC_EGL */" >> $@
	echo -e "#elif defined (_WIN32)\n" >> $@
	$(BIN)/make_info.pl $(WGL_EXT_SPEC) >> $@
	echo -e "#else /* _UNIX */\n" >> $@
	$(BIN)/make_info.pl $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_info.pl $(GLX_EXT_SPEC) >> $@
	echo -e "#endif /* GLEW_USE_LIB_ES */\n" >> $@

	cat $(SRC)/glewinfo_gl.c >> $@
	$(BIN)/make_info_list.pl $(GL_CORE_SPEC) >> $@
	$(BIN)/make_info_list.pl $(GLES_CORE_SPEC) >> $@
	$(BIN)/make_info_list.pl $(GL_EXT_SPEC) >> $@
	$(BIN)/make_info_list.pl $(GLES_EXT_SPEC) >> $@
	cat $(SRC)/glewinfo_egl.c >> $@
	$(BIN)/make_info_list.pl $(EGL_CORE_SPEC) >> $@
	$(BIN)/make_info_list.pl $(EGL_EXT_SPEC) >> $@
	cat $(SRC)/glewinfo_wgl.c >> $@
	$(BIN)/make_info_list.pl $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/glewinfo_glx.c >> $@
	$(BIN)/make_info_list.pl $(GLX_CORE_SPEC) >> $@
	$(BIN)/make_info_list.pl $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/glewinfo_tail.c >> $@
	perl -e 's/"glColorSubTable"/"glBlendEquation", glBlendEquation == NULL);\n  glewInfoFunc("glColorSubTable"/g' -pi $@
	perl -00 -e "s/glewGetExtension\(\"GL_OES_EGL_image_external \"\)\);/glewGetExtension(\"GL_OES_EGL_image_external \"));\n\n\tglewInfoFunc(\"glEGLImageTargetTexture2DOES\", glEGLImageTargetTexture2DOES == NULL);/" -pi $@
	perl -00 -e "s/eglewGetExtension\(\"EGL_KHR_fence_sync\"\)\);/eglewGetExtension(\"EGL_KHR_fence_sync\"));\n\n\tglewInfoFunc(\"eglClientWaitSyncKHR\", eglClientWaitSyncKHR == NULL);\n\tglewInfoFunc(\"eglCreateSyncKHR\", eglCreateSyncKHR == NULL);\n\tglewInfoFunc(\"eglDestroySyncKHR\", eglDestroySyncKHR == NULL);\n\tglewInfoFunc(\"eglGetSyncAttribKHR\", eglGetSyncAttribKHR == NULL); /" -pi $@
	#---remove the common folder and copy back the files---
	$(BIN)/move_back_common_ext.sh $(EXT_ES) $(COMMON)
	rm -f $@.bak

# Update documentation

$(D.DEST)/%.html: doc/%.html
	@echo "--------------------------------------------------------------------"
	@echo "Creating $(@F)"
	@echo "--------------------------------------------------------------------"
	cat $(SRC)/header.html $< $(SRC)/footer.html | \
	perl -pe 's#<a href="$(@F)">(.*)</a>#\1#' > $@

$(D.DEST)/glew.html: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glew.html"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/header.html $@
	echo -e "<h2>Supported OpenGL Extensions</h2>\n" >> $@
	$(BIN)/make_html.pl $(GL_EXT_SPEC) >> $@
	cat $(SRC)/footer.html >> $@
	perl -i -pe 's#<a href="$(@F)">(.*)</a>#\1#' $@

$(D.DEST)/glesew.html: $(EXT_ES)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glesew.html"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/header.html $@
	echo -e "<h2>Supported OpenGL ES Extensions</h2>\n" >> $@
	$(BIN)/make_html.pl $(GLES_EXT_SPEC) >> $@
	cat $(SRC)/footer.html >> $@
	perl -i -pe 's#<a href="$(@F)">(.*)</a>#\1#' $@

$(D.DEST)/wglew.html: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating wglew.html"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/header.html $@
	echo -e "<h2>Supported WGL Extensions</h2>\n" >> $@
	$(BIN)/make_html.pl $(WGL_EXT_SPEC) >> $@
	cat $(SRC)/footer.html >> $@
	perl -i -pe 's#<a href="$(@F)">(.*)</a>#\1#' $@

$(D.DEST)/glxew.html: $(EXT)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating glxew.html"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/header.html $@
	echo -e "<h2>Supported GLX Extensions</h2>\n" >> $@
	$(BIN)/make_html.pl $(GLX_EXT_SPEC) >> $@
	cat $(SRC)/footer.html >> $@
	perl -i -pe 's#<a href="$(@F)">(.*)</a>#\1#' $@

$(D.DEST)/eglew.html: $(EXT_EGL)/.dummy
	@echo "--------------------------------------------------------------------"
	@echo "Creating eglew.html"
	@echo "--------------------------------------------------------------------"
	cp -f $(SRC)/header.html $@
	echo -e "<h2>Supported EGL Extensions</h2>\n" >> $@
	$(BIN)/make_html.pl $(EGL_EXT_SPEC) >> $@
	cat $(SRC)/footer.html >> $@
	perl -i -pe 's#<a href="$(@F)">(.*)</a>#\1#' $@


$(B.DEST)/%.rc: src/%.rc $(EXT)/.dummy
	perl -e "s/GLEW_MAJOR/$(GLEW_MAJOR)/g;s/GLEW_MINOR/$(GLEW_MINOR)/g;s/GLEW_MICRO/$(GLEW_MICRO)/g;" -p $< > $@

clean:
	rm -rf $(I.TARGETS) $(S.TARGETS) $(D.TARGETS) $(B.TARGETS)

clobber: clean
	rm -rf $(EXT) $(EXT_ES) $(EXT_EGL)

destroy: clobber
	rm -rf $(REGISTRY) $(REGISTRY_ES) $(REGISTRY_EGL)
