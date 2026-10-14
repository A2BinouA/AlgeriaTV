algerian_tv/
├── lib/
│   ├── main.dart
│   ├── models/
│   │   └── channel.dart
│   ├── data/
│   │   └── channels_data.dart
│   ├── screens/
│   │   ├── home_screen.dart
│   │   └── player_screen.dart
│   └── widgets/
│       └── channel_card.dart
└── pubspec.yaml
name: algerian_tv
description: Watch Algerian TV channels live
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter
  video_player: ^2.8.2
  chewie: ^1.7.5
  cached_network_image: ^3.3.1
  cupertino_icons: ^1.0.6

flutter:
  uses-material-design: true
  flutter create algerian_tv
cd algerian_tv
# replace pubspec.yaml, then:
flutter pub get
class Channel {
  final String name;
  final String nameAr;
  final String logoUrl;
  final String streamUrl;
  final String category;

  const Channel({
    required this.name,
    required this.nameAr,
    required this.logoUrl,
    required this.streamUrl,
    required this.category,
  });
}
import '../models/channel.dart';

const List<Channel> algerianChannels = [
  Channel(
    name: 'ENTV',
    nameAr: 'التلفزيون الجزائري',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/entv.png',
    streamUrl: 'https://entv-live.dz/stream.m3u8', // replace with working URL
    category: 'National',
  ),
  Channel(
    name: 'Canal Algérie',
    nameAr: 'قناة الجزائر',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/canal-algerie.png',
    streamUrl: 'https://canal-algerie.dz/stream.m3u8',
    category: 'National',
  ),
  Channel(
    name: 'A3',
    nameAr: 'الشروق',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/a3.png',
    streamUrl: 'https://a3.dz/stream.m3u8',
    category: 'Entertainment',
  ),
  Channel(
    name: 'TV6',
    nameAr: 'قناة الرياضة',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/tv6.png',
    streamUrl: 'https://tv6.dz/stream.m3u8',
    category: 'Sports',
  ),
  Channel(
    name: 'Ennahar TV',
    nameAr: 'النهار',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/ennahar.png',
    streamUrl: 'https://ennahar.dz/stream.m3u8',
    category: 'News',
  ),
  Channel(
    name: 'Echorouk News',
    nameAr: 'الشروق نيوز',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/echorouk.png',
    streamUrl: 'https://echorouk.dz/stream.m3u8',
    category: 'News',
  ),
  Channel(
    name: 'El Bilad TV',
    nameAr: 'البلاد',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/elbilad.png',
    streamUrl: 'https://elbilad.dz/stream.m3u8',
    category: 'News',
  ),
  Channel(
    name: 'AL24 News',
    nameAr: 'AL24 نيوز',
    logoUrl: 'https://iptv-org.github.io/iptv/logos/dz/al24.png',
    streamUrl: 'https://al24news.dz/live.m3u8',
    category: 'News',
  ),
];
import 'package:flutter/material.dart';
import 'package:video_player/video_player.dart';
import 'package:chewie/chewie.dart';
import '../models/channel.dart';

class PlayerScreen extends StatefulWidget {
  final Channel channel;
  const PlayerScreen({super.key, required this.channel});

  @override
  State<PlayerScreen> createState() => _PlayerScreenState();
}

class _PlayerScreenState extends State<PlayerScreen> {
  late VideoPlayerController _videoController;
  ChewieController? _chewieController;
  bool _hasError = false;

  @override
  void initState() {
    super.initState();
    _initializePlayer();
  }

  Future<void> _initializePlayer() async {
    try {
      _videoController =
          VideoPlayerController.networkUrl(Uri.parse(widget.channel.streamUrl));
      await _videoController.initialize();

      _chewieController = ChewieController(
        videoPlayerController: _videoController,
        autoPlay: true,
        looping: false,
        isLive: true,
        allowFullScreen: true,
        allowMuting: true,
        showControls: true,
        errorBuilder: (context, errorMessage) => Center(
          child: Text('Stream error: $errorMessage',
              style: const TextStyle(color: Colors.white)),
        ),
      );
      setState(() {});
    } catch (e) {
      setState(() => _hasError = true);
    }
  }

  @override
  void dispose() {
    _videoController.dispose();
    _chewieController?.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.black,
      appBar: AppBar(
        backgroundColor: Colors.black,
        title: Text('${widget.channel.name} — ${widget.channel.nameAr}'),
      ),
      body: Center(
        child: _hasError
            ? const Text('Unable to load stream',
                style: TextStyle(color: Colors.white))
            : _chewieController != null &&
                    _chewieController!.videoPlayerController.value.isInitialized
                ? AspectRatio(
                    aspectRatio: _videoController.value.aspectRatio,
                    child: Chewie(controller: _chewieController!),
                  )
                : const CircularProgressIndicator(),
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'package:video_player/video_player.dart';
import 'package:chewie/chewie.dart';
import '../models/channel.dart';

class PlayerScreen extends StatefulWidget {
  final Channel channel;
  const PlayerScreen({super.key, required this.channel});

  @override
  State<PlayerScreen> createState() => _PlayerScreenState();
}

class _PlayerScreenState extends State<PlayerScreen> {
  late VideoPlayerController _videoController;
  ChewieController? _chewieController;
  bool _hasError = false;

  @override
  void initState() {
    super.initState();
    _initializePlayer();
  }

  Future<void> _initializePlayer() async {
    try {
      _videoController =
          VideoPlayerController.networkUrl(Uri.parse(widget.channel.streamUrl));
      await _videoController.initialize();

      _chewieController = ChewieController(
        videoPlayerController: _videoController,
        autoPlay: true,
        looping: false,
        isLive: true,
        allowFullScreen: true,
        allowMuting: true,
        showControls: true,
        errorBuilder: (context, errorMessage) => Center(
          child: Text('Stream error: $errorMessage',
              style: const TextStyle(color: Colors.white)),
        ),
      );
      setState(() {});
    } catch (e) {
      setState(() => _hasError = true);
    }
  }

  @override
  void dispose() {
    _videoController.dispose();
    _chewieController?.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.black,
      appBar: AppBar(
        backgroundColor: Colors.black,
        title: Text('${widget.channel.name} — ${widget.channel.nameAr}'),
      ),
      body: Center(
        child: _hasError
            ? const Text('Unable to load stream',
                style: TextStyle(color: Colors.white))
            : _chewieController != null &&
                    _chewieController!.videoPlayerController.value.isInitialized
                ? AspectRatio(
                    aspectRatio: _videoController.value.aspectRatio,
                    child: Chewie(controller: _chewieController!),
                  )
                : const CircularProgressIndicator(),
      ),
    );
  }
}
import 'package:flutter/material.dart';
import '../data/channels_data.dart';
import '../widgets/channel_card.dart';
import 'player_screen.dart';

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});

  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  String _selectedCategory = 'All';

  List<String> get _categories {
    final set = {'All', ...algerianChannels.map((c) => c.category)};
    return set.toList();
  }

  @override
  Widget build(BuildContext context) {
    final filtered = _selectedCategory == 'All'
        ? algerianChannels
        : algerianChannels.where((c) => c.category == _selectedCategory).toList();

    return Scaffold(
      appBar: AppBar(
        title: const Text('🇩🇿 Algerian TV'),
        backgroundColor: Colors.green[800],
        foregroundColor: Colors.white,
      ),
      body: Column(
        children: [
          SizedBox(
            height: 50,
            child: ListView.separated(
              scrollDirection: Axis.horizontal,
              padding: const EdgeInsets.symmetric(horizontal: 12, vertical: 8),
              itemCount: _categories.length,
              separatorBuilder: (_, __) => const SizedBox(width: 8),
              itemBuilder: (context, i) {
                final cat = _categories[i];
                return ChoiceChip(
                  label: Text(cat),
                  selected: _selectedCategory == cat,
                  onSelected: (_) => setState(() => _selectedCategory = cat),
                );
              },
            ),
          ),
          Expanded(
            child: GridView.builder(
              padding: const EdgeInsets.all(12),
              gridDelegate:
                  const SliverGridDelegateWithFixedCrossAxisCount(
                crossAxisCount: 2,
                childAspectRatio: 0.85,
                crossAxisSpacing: 12,
                mainAxisSpacing: 12,
              ),
              itemCount: filtered.length,
              itemBuilder: (context, i) {
                final channel = filtered[i];
                return ChannelCard(
                  channel: channel,
                  onTap: () => Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (_) => PlayerScreen(channel: channel),
                    ),
                  ),
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'screens/home_screen.dart';

void main() => runApp(const AlgerianTvApp());

class AlgerianTvApp extends StatelessWidget {
  const AlgerianTvApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Algerian TV',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.green),
        useMaterial3: true,
      ),
      home: const HomeScreen(),
    );
  }
}
<uses-permission android:name="android.permission.INTERNET"/>
android:usesCleartextTraffic="true"
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
flutter run
https://iptv-org.github.io/iptv/countries/dz.m3u
import 'package:http/http.dart' as http;

Future<List<Channel>> loadChannelsFromM3U() async {
  final res = await http.get(
    Uri.parse('https://iptv-org.github.io/iptv/countries/dz.m3u'),
  );
  final lines = res.body.split('\n');
  final channels = <Channel>[];
  String? name, logo;

  for (final line in lines) {
    if (line.startsWith('#EXTINF')) {
      name = RegExp(r',(.+)$').firstMatch(line)?.group(1)?.trim();
      logo = RegExp(r'tvg-logo="([^"]+)"').firstMatch(line)?.group(1);
    } else if (line.startsWith('http')) {
      if (name != null) {
        channels.add(Channel(
          name: name,
          nameAr: name,
          logoUrl: logo ?? '',
          streamUrl: line.trim(),
          category: 'General',
        ));
      }
      name = null;
    }
  }
  return channels;
}
Feature Package
Favorites shared_preferences
EPG (TV guide) XMLTV parser
Chromecast flutter_chromecast
Picture-in-Picture floating
Arabic RTL UI Directionality(textDirection: TextDirection.rtl)
