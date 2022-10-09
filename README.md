<!--
This README describes the package. If you publish this package to pub.dev,
this README's contents appear on the landing page for your package.

For information about how to write a good package README, see the guide for
[writing package pages](https://dart.dev/guides/libraries/writing-package-pages).

For general information about developing packages, see the Dart guide for
[creating packages](https://dart.dev/guides/libraries/create-library-packages)
and the Flutter guide for
[developing packages and plugins](https://flutter.dev/developing-packages).
-->

You can use the PageView keeping position of items in Android and IOS.

## Features

TODO: List what your package can do. Maybe include images, gifs, or videos.

## Getting started

TODO: List prerequisites and provide or point to information on how to
start using the package.

## Usage

TODO: Include short and useful examples for package users. Add longer examples
to `/example` folder.

```dart
import 'package:flutter/material.dart';
import 'package:stack_pageview/stackPageView/stackPageViewInterface.dart';

import 'pageItem/pageItem.dart';
import 'stackPageView/stackPageView.dart';

class Home extends StatefulWidget {
  const Home({Key? key}) : super(key: key);

  @override
  _HomeState createState() => _HomeState();
}

class _HomeState extends State<Home> with SingleTickerProviderStateMixin {
  late TabController _tabController;

  final List<String> _tabLabels = ["탭 A", "탭 B", "탭 C"];

  List<ScrollController> scrollControllers = [];

  StackPageViewInterface? interface;

  @override
  void initState() {
    _tabController = TabController(
        length: _tabLabels.length,
        vsync: this,
        animationDuration: const Duration(milliseconds: 200));
    for (var o in _tabLabels) {
      scrollControllers.add(ScrollController());
    }
    super.initState();
  }

  @override
  void dispose() {
    _tabController.dispose();
    for (int i = 0; i < _tabLabels.length; i++) {
      scrollControllers[i].dispose();
    }
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: StackPageView(
              header: _header(),
              headerHeight: 150,
              tabBar: _tabBar(),
              timerPeriodic: 1,
              tabController: _tabController,
              scrollControllers: scrollControllers,
              interface: (interface) => this.interface = interface,
              tabBarViews: _tabBarView(),
              dragY: 10,
              controller: (controller) {},
              tabBarBackground: _tabDivider(),
              tabBarBackgroundColor: Colors.white,
            ),
          ),
          _button(),
        ],
      ),
    );
  }

  Widget _header() {
    return Container(
      color: Colors.blue,
      height: 150,
      child: const Center(
        child: Text(
          "Header",
          style: TextStyle(fontSize: 30, color: Colors.white),
        ),
      ),
    );
  }

  TabBar _tabBar() {
    return TabBar(
      controller: _tabController,
      indicatorColor: Colors.black,
      labelColor: Colors.black,
      unselectedLabelColor: Colors.grey,
      tabs: List.generate(_tabLabels.length, (index) {
        return SizedBox(
          height: 45,
          child: Center(child: Text(_tabLabels[index])),
        );
      }),
    );
  }

  List<Widget> _tabBarView() {
    return [
      PageItem(
        text: "탭 A",
        scrollController: scrollControllers[0],
      ),
      PageItem(
        text: "탭 B",
        scrollController: scrollControllers[1],
      ),
      PageItem(
        text: "탭 C",
        scrollController: scrollControllers[2],
      ),
    ];
  }

  Widget _button() {
    return Row(
      children: [
        ElevatedButton(
          onPressed: () {
            print('interface : $interface');
            interface?.goTop();
          },
          child: const Text("Go top"),
        ),
        ElevatedButton(
          onPressed: () => interface?.goBottom(),
          child: const Text("Go bottom"),
        ),
      ],
    );
  }

  /// 탭바 하단에 있는 divider widget
  Widget _tabDivider() {
    return const Positioned(
      left: 0,
      right: 0,
      bottom: 0,
      child: Divider(
        height: 2,
        thickness: 2,
        color: Colors.grey,
      ),
    );
  }
}




class PageItem extends StatefulWidget {
  PageItem({
    required this.text,
    required this.scrollController,
    Key? key,
  }) : super(key: key);
  String text;
  ScrollController scrollController;

  @override
  _PageItemState createState() => _PageItemState();
}

class _PageItemState extends State<PageItem> {
  int itemCount = 30;

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      physics: const ClampingScrollPhysics(),
      padding: const EdgeInsets.only(bottom: 100),
      controller: widget.scrollController,
      itemCount: itemCount,
      itemBuilder: (context, index) {
        if (index == itemCount - 1) addData();
        return SizedBox(
          width: double.infinity,
          height: 50,
          child: Text("${widget.text} : ${index.toString()}"),
        );
      },
    );
  }

  addData() async {
    if (!mounted) return;
    await Future.delayed(const Duration(milliseconds: 300));
    setState(() {
      itemCount += 20;
    });
  }
}


```

## Additional information

TODO: Tell users more about the package: where to find more information, how to
contribute to the package, how to file issues, what response they can expect
from the package authors, and more.
