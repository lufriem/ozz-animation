Release version 0.16.0
----------------------

> This version brings breaking changes to \*2ozz json configuration.

* Library
  - [offline] Implements root motion extraction through `ozz::animation::offline::MotionExtraction` utility. Root motion defines how a character moves during an animation. The utility extracts the motion (position and rotation) from a root joint of the animation into separate tracks, and removes (bakes) that motion from the original animation. User code is expected to reapply motion at runtime by moving the character transform, hence reconstructing the original animation.
  - [animation] Implements root motion blending through `ozz::animation::MotionBlendingJob`. With a similar interface to `ozz::animation::BlendingJob`, the job blends (interpolate) the delta of motion from multiple animations according to weight coefficients.
  - [animation] Improves compaction for identity and constant tracks.
  - [base] Make Stream's destructor public. (#183).

* Tools
  - Adds motion track extraction to \*2ozz. Configuration (json) is extended with a animations.tracks.motion object that exposes root motion extraction settings. See [src/animation/offline/tools/reference.json#L67]().
  - Breaking \*2ozz json configuration change. \*2ozz json configuration animations.tracks is no longer an array, but now an object. See [src/animation/offline/tools/reference.json#L51]().
  - Updates nlohmann/json from 3.5.0 to 3.11.3 (#179).
  - Logs \*2ozz configuration in verbose log level.

* Samples
  - Adds [motion extraction sample](https://guillaumeblanc.github.io/ozz-animation/samples/motion_extraction/), showcasing root motion extraction parameters.
  - Adds [motion playback sample](https://guillaumeblanc.github.io/ozz-animation/samples/motion_playback/), demonstrating motion accumulation during playback.
  - Adds [motion blending sample](https://guillaumeblanc.github.io/ozz-animation/samples/motion_blend/), leveraging `ozz::animation::MotionBlendingJob` to blend root motion of three animations.

Release version 0.15.0
----------------------

> This version brings breaking changes that require rebuilding runtime animations.

* Library
  - [offline/animation] #152 Fixes additive animation blending issues due to wrong quaternion multiplication order in additive animation builder and blending job. Note that additive animation needs to be rebuilt to benefit from the fix.
  - [animation] Restructures compressed keyframes to allow fast sequential backward reading. The per keyframe track index has been removed, replaced by an offset to the previous keyframe (for the same track). This offset is used to identify tracks when reading forward, and jump to previous keyframe when reading backward.
  - [animation] Keyframe times are now indexed. This removes 16b to 24b from every keyframe (depending on animation duration), aka 17% to 25% of the keyframe size. The overhead of storing timepoints is shared for translations, rotations and scales.
  - [animation] Leverages animation format changes to refactors serialization. Animations serialization now uses arrays of keyframes which significantly reduces the number of io::Stream reads.
  - [animation] Adds iframe support (aka keyframe snapshots), allowing fast seeks into the animation thanks to precomputed (and compressed) sampling cache states. The sampling job decides automatically when an iframe is used to seek into the animation, based on seek offset.
  - [offline] Extends animation importer configuration to allow setting up iframe_interval. An interval of 0 (or less) means no iframe is generated. If interval is positive, then at least an iframe is generated at animation end.
  - [base] Implements group varint encoding utility. It's used to compress iframes.

* Build pipeline
  - Adds CI for WebAssembly.
  - Adds support for macOS ARM.

Release version 0.14.3
----------------------

* Build pipeline
  - Adds vs2022 compiler support for fbxsdk (#170)

Release version 0.14.2
----------------------

* Library
  - Transitions away from sprintf to the more secure snprintf.
  - #147 Works around gcc 11 error stringop-overflow which emits false positives for ozz math serialization.

* Build pipeline
  - Updates CI compiler versions.

Release version 0.14.1
----------------------

* Samples
  - Allows reusing sample framework outside of sample directory.
  - #154 Exposes swap interval

* Library
  - #153 Fixes deprecated implicit copy warning.
  - #141 Removes non-ASCII characters in source codes.

* Build pipeline
  - Exposes ozz cmake configuration variables to PARENT_SCOPE, so it can be used/changed by an external project.

Release version 0.14.0
----------------------

* Samples
  - [sample_fbx2mesh] Supports vertices part of a skinned mesh but with a weight of 0.
  - [sample_fbx2mesh] Assigns non-influenced vertices to root joint.

* Library
  - [offline] #124 Fixes incorrect root joint detection bug in glTF importer.
  - [offline] #129 #130 Copy animation name to output in `ozz::animation::offline::AdditiveAnimationBuilder`.
  - [animation] #103 Allows move constructor and assignment for `ozz::animation::Skeleton`, `ozz::animation::Animation` and `ozz::animation::Track`.
  - [animation] Renames SamplingCache to `SamplingJob::Context`.
  - [animation] #110 Renames skeleton bind pose to rest pose, to avoid confusion with skinning bind pose.
  - [offline] Extends configuration to allow setting up iframe time interval.  
  - [base] Fixes Float4x4::FromEuler which was swapping pitch and roll.

* Build pipeline
  - Moves CI to github actions.
  - #59 Adds support for shared libraries on Windows (dll), Linux and MacOS platforms.
  - #111 Removes _GLIBCXX_DEBUG from default build settings as it can create incompatibilities when using prebuilt packages.
  - #122 #137 Adds support for gcc 11 compiler.

Release version 0.13.0
----------------------

* Tools
  - [gltf2ozz] Command line tool utility to import animations and skeletons from glTF files. gltf2ozz can be configured via command line options and [json configuration files](src/animation/offline/tools/reference.json), in the exact same way as fbx2ozz.
  - #91 Fixup animation name when used as an output filename (via json configuration wildcard option), so they comply with most os filename restrictions.

* Samples
  - [skinning] Adds a new sample to explain skinning matrices setup.

* Build pipeline
  - Enables c++11 feature by default for all targets.

* Library
  - [animation] Removes skeleton_utils.h IterateMemFun helper that can be replaced by `std::bind`.
  - [base] Removes `ozz::memory::Allocator::Reallocate()` function as it's rarely used and complex to overload.
  - [base] Replaces OZZ_NEW and OZZ_DELETE macros with template functions `ozz::New` and `ozz::Delete`.
  - [base] Removes ScopedPtr in favor of an alias to standard unique_ptr that remaps to ozz deallocator. Implements make_unique using ozz allocator.
  - [base] Uses template aliasing (using keyword) to redirect ozz to std containers. This allows to get rid of ::Std when using ozz containers.
  - [base] Renames all aliased ozz containers to their original std name: vector, map etc... 
  - [base] Renames `ozz::Range` to `ozz::span`, `ozz::make_range` to `ozz::make_span` to comply with std containers. Range count() and size() methods are renamed to size() and size_bytes() respectively, so this needs special attention to avoid mistakes.
  - [base] Replaces OZZ_ALIGN_OF and OZZ_ALIGN by standard alignof and alignas keywords.
  - [base] Replaces OZZ_STATIC_ASSERT by standard static_assert keyword.

Release version 0.12.1
----------------------

* Library
  - [base] Fixes memory overwrite when reallocating a buffer of smaller size using ozz default memory allocator.

Release version 0.12.0
----------------------

* Library
  - [offline] Simplified AnimationOptimizer settings to a single tolerance and distance. Tolerance is the maximum error that an optimization is allowed to generate on a whole joint hierarchy, while the other parameter is the distance (from the joint) at which error is measured. This second parameter allows to emulate effect on skinning or a long object (sword) attached to a joint (hand).
  - [offline] Adds options to AnimationOptimizer to override optimization settings for a joint. This implicitly have an effect on the whole chain, up to that joint. This allows for example to have aggressive optimization for a whole skeleton, except for the chain that leads to the hand if user wants it to be precise.
  - [offline] Switches AnimationOptimizer and TrackOptimizer to Ramer–Douglas–Peucker decimation algorithm which delivers better precision than original one. It also ensures that first and last points are preserved, avoiding visual issues for looping animations.
  - [offline] Changes order of parameters for IterateJointsDF so it's less error prone.
  - [memory] Implements ScopedPtr smart pointer. ScopedPtr implementation guarantees the pointed object will be deleted, either on destruction of the ScopedPtr, or via an explicit reset / reassignation.
  - [math] Quaternion compare function now takes cosine of half angle as argument, to avoid computing arc cosine for every comparison as the tolerance is usually constant.
  - [base] Replaces `ozz::memory::Allocator` New and Delete function with OZZ_NEW and OZZ_DELETE macros, in order to provide an interface that supports any type and number of arguments (without requiring c++11).
  - [base] #83 Allows user code to disable definition of global namespace sse \_m128 +-/* operators, as they might conflict with other sse math libraries.

* Samples
  - [optimize] Exposes joint setting overriding option (from AnimationOptimizer) to sample gui. Displays median and maximum error values.

* Tools
  - Moves keyframe reduction stage for additive animations before computing delta with reference pose. This ensures whole skeleton hierarchy is known before decimating keyframes.

Release version 0.11.0
----------------------

* Library
  - [animation] Adds two-bone and aim inverse kinematic solvers. They can be used at runtime to procedurally affect joint local-space transforms.
  - [animation] Allows resizing `SamplingJob::Context`, meaning the can be allocated without knowing the number of joints the cache needs to support.
  - [animation] Allow `ozz::animation::LocalToModelJob` to partially update a hierarchy, aka all children of a joint. This is useful when changes to a local-space pose has been limited to part of the joint hierarchy, like when applying IK or modifying model-space matrices independently from local-space transform.
  - [animation] Changes `ozz::animation::Skeleton` joints from breadth-first to depth-first. This change breaks compatibility of previous `ozz::animation::offline::RawAnimation`, `ozz::animation::Animation` and `ozz::animation::Skeleton` archives.
  - [animation] Renames track_triggering_job_stl.h to track_triggering_job_trait.h.
  - [offline] #62 Adds an a way to specify additive animation reference pose to `ozz::animation::offline::AdditiveAnimationBuilder`.
  - [memory] Removes (too error prone) `ozz::memory::Allocator` typed allocation functions.
  - [math] Changes all conversion from AxisAngle to use separate arguments for axis and angle. This is more in line with function use cases.
  - [math] Adds quaternions initialization from two vectors.
  - [simd math] Updates simd math functions to prevent unnecessary operations. Some functions now return undefined values for some components, like Dot3 that will return the dot value in x and undefined values for x, y, z. See [simd_math.h](include/ozz/base/maths/simd_math.h) for each function documentation.
  - [simd math] Implements AVX and FMA optimizations (when enabled at compile time).
  - [simd math] Implements simd quaternions, making it easier and more efficient to use quaternion with other simd math code.
  - [simd math] Exposes swizzling operations.

* Samples
  - [two bone ik] Adds two-bone ik sample, showing how `ozz::animation::IKTwoBoneJob` can be used on a robot arm.
  - [look at] Adds a look-at sample, using `ozz::animation::IKAimJob` on a chain of bones to distribute aiming contribution to more than a single joint.
  - [foot_ik] Adds foot-ik sample, which corrects character legs and ankles procedurally at runtime, as well as character/pelvis height, so that the feet can touch and adapt to the ground.

* Build pipeline
  - Adds support for fbx sdk 2019. This version is now mandatory for vs2017 builds.
  - Adds support to macos 10.14 Mojave and Xcode 10.0.

* Tools
  - Adds point and vector property types (used to import tracks). These two types are actually float3 types, with scene axis and unit conversion applied.
  - Adds an option to importer tools to select additive animation reference pose.

* Build pipeline
  - #40 Adds ozz_build_postfix option.
  - #41 Adds ozz_build_tools option.

Release version 0.10.0
----------------------

* Library
  - [animation] Adds user-channel feature #4. ozz now offers tracks of float, float2, float3, float4 and quaternion for both raw/offline and runtime. A track can be used to store animated user-data, aka data that aren't joint transformations. Runtime jobs allow to query a track value for any time t (`ozz::animation::TrackSamplingJob`), or to find all rising and falling edges that happened during a period of time (`ozz::animation::TrackTriggeringJob`). Utilities allow to optimize a raw track (`ozz::animation::offline::TrackOptimizer`) and build a runtime track (`ozz::animation::offline::TrackOptimizer`). fbx2ozz comes with the ability to import tracks from fbx node properties.
  - [animation] Changed `ozz::animation::SamplingJob::time` (in interval [0,duration]) to a ratio (in unit interval [0,1]). This is a breaking change, aiming to unify sampling of animations and tracks. To conform with this change, sampling time should simply be divided by animation duration. `ozz::sample:AnimationController` has been updated accordingly. Offline animations and tools aren't impacted.
  - [base] Changes non-intrusive serialization mechanism to use a specialize template struct "Extern" instead of function overloading.

* Tools
  - Merged \*2skel and \*2anim in a single tool (\*2ozz, fbx2ozz for fbx importer) where all options are specified as a json config file. List of options with default values are available in [src/animation/offline/tools/reference.json](src/animation/offline/tools/reference.json) file. Consequently, ozz_animation_offline_skel_tools and ozz_animation_offline_anim_tools are also merged into a single ozz_animation_tools library.
  - Adds options to import user-channel tracks (from node properties for fbx) using json "animations[].tracks[].properties[]" definition.
  - Adds an option while importing skeletons to choose scene node types that must be considered as skeleton joints, ie not restricting to scene joints only. This is useful for the baked sample for example, which animates mesh nodes.

* Build pipeline
  - ozz optionnaly supports c++11 compiler.
  - Adds ozz_build_data option (OFF by default), to avoid building data on every code change. Building data takes time indeed, and isn't required on every change. It should be turned ON when output format changes to update all data again.
  - Removes fused source files from the depot. Fused files are generated during build to ${PROJECT_BINARY_DIR}/src_fused/ folder. To generate fused source files without building the whole project, then build BUILD_FUSE_ALL target with "cmake --build . --target BUILD_FUSE_ALL" command.
  - Adds support for Visual Studio 15 2017, drops Visual Studio 11 2012.

* Samples
  - [user_channel] Adds new user-channel sample, demonstrating usage of user-channel tracks API and import pipeline usage.
  - [sample_fbx2mesh] Remaps joint indices to the smaller range of skeleton joints that are actually used by the skinning. It's now required to index skeleton matrices using `ozz::sample::framework:Mesh::joint_remaps` when build skinning matrices.
  - [multithread] Switched from OpenMP to c++11 `std::async` API to implement a parallel-for loop over all computation tasks.
  
Release version 0.9.1
---------------------

* Build pipeline
  - Allows to use ozz-animation from another project using cmake add_subdirectory() command, conforming with [online documentation](http://guillaumeblanc.github.io/ozz-animation/documentation/build/).
  - Adds Travis-CI (http://travis-ci.org/guillaumeblanc/ozz-animation) and AppVeyor (http://ci.appveyor.com/project/guillaumeblanc/ozz-animation) continuous integration support.
  - Exposes MSVC /MD and /MT option (ozz_build_msvc_rt_dll). Default is /MD, same as MSVC/cmake.
  - Adds support for Xcode 8.3.2 (fbx specific compilation option).

Release version 0.9.0
---------------------

* Library
  - [offline] Removes dae2* tools, offline libraries and dependencies. Collada is still supported through fbx2* tools suite, as they are based on the Autodesk Fbx sdk.
  - [offline][animation] Adds a name to the `offline::RawAnimation` and Animation data structure. `ozz::animation::Animation` serialization format has changed, animations generated with a previous version need to be re-built.
  - [animation] Optimizes animation and skeleton allocation strategy, merging all member buffers to a single allocation.
  - [animation] Fixes memory read overrun in `ozz::animation::Skeleton` while fixing up skeleton joint names.
  - [offline] #5 Allows importing of all animations from a DCC file with a single command. fbx2anim now support the use of the wildcard character '*' in the --animation option (output file name), which is replaced with the imported animation names when the output file is written to disk.
  - [offline] Uses scene frame rate as the default sampling rate option in fbx2anim. Allows to match DCC keys and avoid interpolation issues while importing from fbx sdk.
  - [base] Adds support for Range serialization (as an array) via `ozz::io::MakeArray` utility.
  - [base] Fixes SSE matrix decomposition implementation which wasn't able to decompose matrices with very small scales.

* Build pipeline
  - Rework build pipeline and media directory to allow to have working samples even if Fbx sdk isn't available to build fbx tools. Media directory now contains pre-built binary data for samples usage.
  - A Fused version of the sources for all libraries can be found in src_fused folder. It is automatically generated by cmake when any library source file changes.
  - #20 FindFbx.cmake module now supports a version as argument which can be tested in conformance with cmake find_package specifications.
  - Adds a clang-format target that re-formats all sdk sources. This target is not added to the ALL_BUILD target. It must be ran manually.
  - Removes forced MSVC compiler options (SSE, RTC, GS) that are already properly handled by default.

* Samples
  - [baked] Adds new baked physic sample. Demonstrates how to modify Fbx skeleton importer to build skeletons from custom nodes, and use an animation to drive rigid bodies.
  - [sample_fbx2mesh] Fixes welding of redundant vertices. Re-imported meshes now have significantly less vertices.
  - [sample_fbx2mesh] Adds UVs, tangents and vertex color to `ozz::sample::framework::Mesh` structure. Meshes generated with a previous version need to be re-built.
  - [framework] Fixes sample first frame time, setting aside time spent initializing.
  - [framework] Supports emscripten webgl canvas resizing.

Release version 0.8.0
---------------------
 
* Library
  - [animation] Adds additive blending support to `ozz::animation::BlendingJob`. Animations used for additive blending should be delta animations (relative to the first frame). Use `ozz::animation::offline::AdditiveAnimationBuilder` to prepare such animations.
  - [animation] Improves quaternion compression scheme by quantizing the 3 smallest components of the quaternion, instead of the firsts 3. This improves numerical accuracy when the restored component (4th) is small. It also allows to pre-multiply each of the 3 smallest components by sqrt(2), maximizing quantization range by over 41%.
  - [offline] Improves animation optimizer process (`ozz::animation::offline::AnimationOptimizer`) with a new hierarchical translation tolerance. The optimizer now computes the error (a distance) generated from the optimization of a joint on its whole child hierarchy (like the whole arm length and hand when optimizing the shoulder). This provides a better optimization in both quality and quantity.
  - [offline] Adds `ozz::animation::offline::AdditiveAnimationBuilder` utility to build delta animations that can be used for additive blending. This utility processes a raw animation to calculate the delta transformation from the first key to all subsequent ones, for all tracks.
  - [offline] Adds --additive option to dae2anim and fbx2anim, allowing to output a delta animation suitable for additive blending.
  - [offline] Adds fbx 20161.* sdk support.

* Build pipeline
  - Adds c++11 build option for gcc/clang compilers. Use cmake ozz_build_cpp11 option.
  - Automatically detects SIMD implementation based on compiler settings. SSE2 implementation is automatically enabled on x64/amd64 targets, or if /arch:SSE2 is selected on MSVC/x86 builds. One could use ozz_build_simd_ref cmake option (OZZ_BUILD_SIMD_REF preprocessor directive) to bypass detection and force reference (aka scalar) implementation. OZZ_HAS_SSE2 is now deprecated.
  - Fixes #3 gcc5 warnings with simd math reference implementation.
  - Fixes #6 by updating to gtest 1.70 to support new platforms (FreeBSD...).
  - Adds Microsoft Visual Studio 14 2015 support.
  - Adds emscripten 1.35 support.
  - Integrate Coverity static analysis (https://scan.coverity.com/projects/guillaumeblanc-ozz-animation).

* Samples
  - [additive] Adds an additive blending sample which demonstrates the new additive layers available through the `ozz::animation::BlendingJob`.
  - [optimize] Adds hierarchical translation tolerance parameter to the optimize sample.
  - [skin] Removes sample skin, as from now on skinning is part of the sample framework and used by other samples. See additive sample.

Release version 0.7.3
---------------------

* Changes license to the MIT License (MIT).

Release version 0.7.2
---------------------

* Library
  - [animation] Improves rotations accuracy during animation sampling and blending. Quaternion normalization now uses one more Newton-Raphson step when computing inverse square root.
  - [offline] Fixes fbx animation duration when take duration is different to timeline length.
  - [offline] Bakes fbx axis/unit systems to all transformations, vertices, animations (and so on...), instead of using fbx sdk ConvertScene strategy which only transform scene root. This allows to mix skeleton, animation and meshes imported from any axis/unit system.
  - [offline] Uses bind-pose transformation, instead of identity, for skeleton joints that are not animated in a fbx file.
  - [offline] Adds support to ozz fbx tools (fbx2skel and fbx2anim) for other formats: Autodesk AutoCAD DXF (.dxf), Collada DAE (.dae), 3D Studio 3DS (.3ds)  and Alias OBJ (.obj). This toolchain based on fbx sdk will replace dae toolchain (dae2skel and dae2anim) which is now deprecated.

* HowTos
  - Adds file loading how-to, which demonstrates how to open a file and deserialize an object with ozz archive library.
  - Adds custom skeleton importer how-to, which demonstrates RawSkeleton setup and conversion to runtime skeleton.
  - Adds custom animation importer how-to, which demonstrates RawAnimation setup and conversion to runtime animation.

* Samples
  - [skin] Skin sample now uses the inverse bind-pose matrices imported from the mesh file, instead of the one computed from the skeleton. This is a more robust solution, which furthermore allow to share the skeleton with meshes using different bind-poses.
  - [skin] Fixes joints weight normalization when importing fbx skin meshes.
  - [framework] Optimizes dynamic vertex buffer object re-allocation strategy.

Release version 0.7.1
---------------------

* Library
  - [offline] Updates to fbx sdk 2015.1 with vs2013 support.
  
* Samples
  - Adds sample_skin_fbx2skin to binary packages.

Release version 0.7.0
---------------------

* Library
  - [geometry] Adds support for matrix palette skinning transformation in a new ozz_geometry library.
  - [offline] Returns EXIT_FAILURE from dae2skel and fbx2skel when no skeleton found in the source file.
  - [offline] Fixes fbx axis and unit system conversions.
  - [offline] Removes raw_skeleton_archive.h and raw_animation_archive.h files, moving serialization definitions back to raw_skeleton.h and raw_animation.h to simplify understanding.
  - [offline] Removes skeleton_archive.h and animation_archive.h files, moving serialization definitions back to skeleton.h and animation.h.
  - [base] Changes Range<>::Size() definition, returning range's size in bytes instead of element count.

* Samples
  - Adds a skinning sample which demonstrates new ozz_geometry SkinningJob feature and usage.

Release version 0.6.0
---------------------

* Library
  - [animation] Compresses animation key frames memory footprint. Rotation key frames are compressed from 24B to 12B (50%). 3 of the 4 components of the quaternion are quantized to 2B each, while the 4th is restored during sampling. Translation and scale components are compressed to half float, reducing their size from 20B to 12B (40%).
  - [animation] Changes `runtime::Animation` class serialization format to support compression. Serialization retro-compatibility for this class has not been implemented, meaning that all `runtime::Animation` must be rebuilt and serialized using usual dae2anim, fbx2anim or using `offline::AnimationBuilder` utility.
  - [base] Adds float-to-half and half-to-float conversion functions to simd math library.

Release version 0.5.0
---------------------

* Library
  - [offline] Adds --raw option to *2skel and *2anim command line tools. Allows to export raw skeleton/animation object format instead of runtime objects.
  - [offline] Moves RawAnimation and RawSkeleton from the builder files to raw_animation.h and raw_skeleton.h files.
  - [offline] Renames skeleton_serialize.h and animation_serialize.h to skeleton_archive.h and animation_archive.h for consistency.
  - [offline] Adds RawAnimation and RawSkeleton serialization support with ozz archives.
  - [options] Changes parser command line arguments type to "const char* const*" in order to support implicit casting from arguments of type "char**".
  - [base] Change `ozz::String` std redirection from typedef to struct to be coherent with all other std containers redirection.
  - [base] Moves maths archiving file from ozz/base/io to ozz/base/maths for consistency.
  - [base] Adds containers serialization support with ozz archives.
  - [base] Removes ozz fixed size integers in favor of standard types available with <stdint.h> file.

* Samples
  - Adds Emscripten support to all supported samples.
  - Changes OpenGL rendering to comply with Gles2/WebGL.

* Build pipeline
  - Adds Emscripten and cross-compilation support to the builder helper python script.
  - Support for CMake 3.x.
  - Adds support for Microsoft Visual Studio 2013.
  - Drops support for Microsoft Visual Studio 2008 and olders, as a consequence of using <stdint.h>.

Release version 0.4.0
---------------------

* Library
  - [offline] Adds Fbx import pipeline, through fbx2skel and fbx2anim command line tools.
  - [offline] Adds Fbx import and conversion library, through ozz_animation_fbx. Building fbx related libraries requires fbx sdk to be installed.
  - [offline] Adds ozz_animation_offline_tools library to share the common work for Collada and Fbx import tools. This could be use to implement custom conversion command line tools.

* Samples
  - Adds Fbx resources to media path.
  - Makes use of Fbx resources with existing samples.

Release version 0.3.1
---------------------

* Samples
  - Adds keyboard camera controls.

* Build pipeline
  - Adds Mac OSX support, full offline and runtime pipeline, samples, dashboard...
  - Moves dashboard to http://ozz.qualipilote.fr/dashboard/cdash/
  - Improves dashboard configuration, using json configuration files available there: http://ozz.qualipilote.fr/dashboard/config/.

Release version 0.3.0
---------------------

* Library
  - [animation] Adds partial animation blending and masking, through per-joint-weight blending coefficients.
  - [animation] Switches all explicit [begin,end[ ranges (sequence of objects) to `ozz::Range` structure.
  - [animation] Moves runtime files (.h and .cc) to a separate runtime folder (ozz/animation/runtime).
  - [animation] Removes ozz/animation/utils.h and .cc
  - [options] Detects duplicated command line arguments and reports failure. 
  - [base] Adds helper functions to `ozz::memory::Allocator` to support allocation/reallocation/deallocation of ranges of objects through `ozz::Range` structure.

* Samples
  - Adds partial animation blending sample.
  - Adds multi-threading sample, using OpenMp to distribute workload.
  - Adds a sample that demonstrates how to attach an object to animated skeleton joints.
  - Improves skeleton rendering sample utility feature: includes joint rendering.
  - Adds screen-shot and video capture options from samples ui.
  - Adds a command line option (--render/--norender) to enable/disable rendering of sample, used for dashboard unit-tests.
  - Adds time management options, to dissociate (fix) update delta time from the real application time.
  - Improves camera manipulations: disables auto-framing when zooming/panning, adds mouse wheel support for zooming.
  - Fixes sample camera framing to match rendering field of view.

* Build pipeline
  - Adds CMake python helper tools (build-helper.py). Removes helper .bat files (setup, build, pack...).
  - Adds CDash support to generate nightly build reports. Default CDash server is http://my.cdash.org/index.php?project=Ozz.
  - Adds code coverage testing support using gcov.

Release version 0.2.0
---------------------

* Library
  - [animation] Adds animation blending support.
  - [animation] Sets maximum skeleton joints to 1023 (aka `Skeleton::kMaxJointsNumBits`) to improve packing and allow stack allocations.
  - [animation] Adds `Skeleton::kRootIndex` enum for parent index of a root joint.
  - [base] Adds signed/unsigned bit shift functions to simd library.
  - [base] Fixes SSE build flags for Visual Studio 64b builds.

* Samples
  - Adds blending sample.
  - Adds playback controller utility class to the sample framework.

Release version 0.1.0, initial open source release
--------------------------------------------------

* Library
  - [animation] Support for run-time animation sampling.
  - [offline] Support for building run-time animation and skeleton from a raw (aka offline/user friendly) format.
  - [offline] Full Collada import pipeline (lib and tools).
  - [offline]  Support for animation key-frame optimizations.
  - [base] Memory management redirection.
  - [base] Containers definition.
  - [base] Serialization and IO utilities implementation.
  - [base] scalar and vector maths, SIMD (SSE) and SoA implementation.
  - [options] Command line parsing utility.

* Samples
  - Playback sample, loads and samples an animation.
  - Millipede sample, offline pipeline usage.
  - Optimize sample, offline optimization pipeline.

* Build pipeline
  - CMake based build pipeline.
  - CTest/GTest based unit test framework.
