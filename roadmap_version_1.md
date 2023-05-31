Dưới đây là đoạn code được format lại trong markdown:

kotlin

```kotlin
private fun drawRoute(roadmaps: List<Roadmap>, listImage: List<String>) {
    val routeView = binding.routeView
    val numColumns = 2.3f // có 4 cột, lấy 2 để căn giữa
    val backgrounds = listImage as MutableList
    var countRoute = roadmaps.size
    val widthRouteBlock = 112f
    val heightRouteBlock = 87f
    val divisorWithFour = countRoute / 4
    val countRouteOriginal = countRoute

    countRoute = if (countRoute % 4 == 0) {
        if (divisorWithFour % 3 == 0) {
            countRouteOriginal
        } else {
            (divisorWithFour / 3 + 1) * 3 * 4
        }
    } else {
        (divisorWithFour / 3 + 1) * 3 * 4
    }

    // Thực hiện loop để vẽ từng block theo logic đã định nghĩa
    // for (i in roadmaps.indices) {
    for (i in 0..roadmaps.size + 1) {
        var trailingConstant: Float
        var topConstant: Float
        val divisor = i / 3
        val surplus = i % 3
        val surplusDivideTwo = divisor % 2

        val screenWidth = requireContext().getScreenWidth()
        var horizontalSpacing =
            (screenWidth - (numColumns * widthRouteBlock)) / (numColumns + 1)
        horizontalSpacing -= 50 / 4

        if (surplusDivideTwo == 0) {
            trailingConstant = -horizontalSpacing - 50 * surplus
            topConstant = 120f + 28 * i
        } else {
            trailingConstant = -horizontalSpacing - 50 * (3 - surplus)
            topConstant = 120f + 28 * i
        }

        val cubeView = RouteCubeView(requireContext(), null)

        if (i < roadmaps.size + 1 && i > 0) {
            val type = when {
                roadmaps[i - 1].progress == 0 -> TypeCompleteRoadmapDay.NO_STATUS
                roadmaps[i - 1].progress!! >= 70 -> TypeCompleteRoadmapDay.DONE
                else -> TypeCompleteRoadmapDay.DOING
            }

            val num = ((roadmaps.size + 1) - i)
            // if (i % 10 == 0) {
            if (num % 10 == 0) {
                cubeView.setupImage(type.getRouteCubeBg(true))
                cubeView.showStar(type.starBg)
                cubeView.setupLb("")
            } else {
                cubeView.setupImage(type.routeCubeBg)
                cubeView.setupLb(
                    ((roadmaps.size + 1) - i).toString(),
                    textColor = type.textColor
                )
            }

        }

        /**
         * 3 block: star, end, study
         */
        val cubeViewLayoutParams = RelativeLayout.LayoutParams(
            (widthRouteBlock * 3.5).toInt(),
            (heightRouteBlock * 3.5).toInt(),
        )
        cubeViewLayoutParams.addRule(RelativeLayout.ALIGN_PARENT_END)

        when (i) {
            0 -> {
                // block end
                val cubeViewFinish = RouteCubeView(requireContext(), null)
                cubeViewFinish.setupImageType(TypeComponentRouteStudy.BLOCK_END)
                cubeViewFinish.setupLb("")

                cubeViewLayoutParams.width = 120 * 4
                cubeViewLayoutParams.height = 180 * 4
                cubeViewFinish.layoutParams = cubeViewLayoutParams
                routeView.addView(cubeViewFinish)
                cubeViewFinish.x =
                    routeView.width - cubeView.width + (trailingConstant * 4).toFloat()
                cubeViewFinish.y = (topConstant * 2).toFloat()
            }

            roadmaps.size + 1 -> {
                // block start
                val cubeViewStart = RouteCubeView(requireContext(), null)
                cubeViewStart.setupImageType(TypeComponentRouteStudy.BLOCK_START)
                cubeViewStart.setupLb("")

                cubeViewLayoutParams.width = 112 * 4
                cubeViewLayoutParams.height = 180 * 4
                cubeViewStart.layoutParams = cubeViewLayoutParams
                routeView.addView(cubeViewStart)
                cubeViewStart.x =
                    routeView.width - cubeView.width + (trailingConstant * 3.7).toFloat()
                cubeViewStart.y = (topConstant * 3.4).toFloat()
            }

            else -> {
                // block_gray
                cubeView.layoutParams = cubeViewLayoutParams
                routeView.addView(cubeView)
                cubeView.x =
                    routeView.width - cubeView.width + (trailingConstant * 3.5).toFloat()
                cubeView.y = (topConstant * 3.5).toFloat()

                cubeView.setOnClickWithAnimation() {
                    // onclick item
                    val bottomSheetDialog = BottomSheetRoute.newInstance(roadmaps[i - 1])
                    childFragmentManager.showBottomSheet(bottomSheetDialog)
                    toast("i: ${i - 1}")
                }
            }
        }
    }

    // Calculate the total height of the routeView
    val cubeViewHeight = 87
    val cubeViewMargin = 28
    val totalHeight = countRoute * cubeViewHeight + (countRoute - 1) * cubeViewMargin

    val layoutParams = routeView.layoutParams
    layoutParams.height = totalHeight
    routeView.layoutParams = layoutParams
}
```
