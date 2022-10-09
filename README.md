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
                      
플러터에서 헤더가 있는 페이지뷰 형태를 구현하는 것은 생각보다 쉽지 않다.
특정 페이지 아이템에서 스크롤을 위아래로 움직이면 다른 페이지 아이템까지 위아래로 스크롤 되는 문제가 있다.
그리고 탭을 전환했을 때 각 페이지에서의 포지션이 유지가 안되고 초기화 되기도 한다.
StackPageView는 이러한 문제를 해결하기 위해 만들어졌다.
               

## Video


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

  final List<String> _tabLabels = ["Tab A", "Tab B", "Tab C"];

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
      appBar: AppBar(
        title: const Text("StackPageView"),
        elevation: 0,
      ),
      body: Column(
        children: [
          Expanded(
            child: StackPageView(
              header: _header(),
              headerHeight: 100,
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
      height: 100,
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
        text: "Tab A",
        scrollController: scrollControllers[0],
      ),
      PageItem(
        text: "Tab B",
        scrollController: scrollControllers[1],
      ),
      PageItem(
        text: "Tab C",
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

