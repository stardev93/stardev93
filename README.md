# Hi there, I'm Olaf 👋

## Full-Stack & Mobile Developer | Multi-Stack Specialist

I build web and mobile applications across diverse technology stacks, specializing in creating scalable, maintainable solutions with excellent user experiences.

### 🛠️ My Tech Stack
- **Mobile**: Flutter
- **Frontend**: React, Vue.js, Angular, TypeScript, JavaScript, HTML/CSS
- **Backend**: Node.js, Express, Laravel, Django, Ruby on Rails.
- **Databases**: MongoDB, MySQL, PostgreSQL
- **DevOps**: Git, Docker, AWS
- **Tools**: Postman, VS Code, Figma, Jira

### Contact
📧 Email: stardevelop93@gmail.com
📧 Telegram: olaf1021

### 💻 Code Sample Showcase
```Flutter
import 'package:flutter/cupertino.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:intl/intl.dart';
import 'package:jp_learning/app/environments/storage.service.dart';
import 'package:jp_learning/entities/today_learning/model/today_learning.dart';
import 'package:jp_learning/entities/today_setting/provider/today_setting_repository.dart';
import 'package:jp_learning/pages/Learning_flow/providers/viewmodel.dart';
import 'package:jp_learning/pages/Learning_flow/screens/learning_flow.dart';
import 'package:jp_learning/pages/Learning_flow/widgets/select_book.dart';
import 'package:jp_learning/pages/tabs/ui/custom_header.dart';
import 'package:jp_learning/shared/screen/loading_screen.dart';
import 'package:jp_learning/widgets/dashboard/screen/flash_card.dart';
import 'package:jp_learning/widgets/dashboard/screen/learning_card.dart';
import 'package:jp_learning/widgets/dashboard/screen/review_card.dart';
import 'package:jp_learning/widgets/dashboard/screen/study_activity_card.dart';

class DashboardPage extends ConsumerStatefulWidget {
  const DashboardPage({super.key});

  @override
  DashboardPageState createState() => DashboardPageState();
}

class DashboardPageState extends ConsumerState<DashboardPage> {
  @override
  void initState() {
    super.initState();
  }

  String getFormattedDate() {
    DateTime now = DateTime.now();
    DateFormat formatter = DateFormat('EEEE, MMMM d');
    String formattedDate = formatter.format(now);
    return formattedDate;
  }

  int getDayDifference(DateTime date1, DateTime date2) {
    final utcDate1 = DateTime.utc(date1.year, date1.month, date1.day);
    final utcDate2 = DateTime.utc(date2.year, date2.month, date2.day);
    final difference = utcDate1.difference(utcDate2);
    return difference.inDays;
  }

  String convertDateFormat(String inputDate) {
    try {
      final DateTime dateTime = DateFormat('yyyy-M-d').parse(inputDate);

      final String formattedDate = DateFormat('M/d,yyyy').format(dateTime);

      return formattedDate;
    } catch (e) {
      print('Error converting date format: $e');
      return inputDate;
    }
  }

  void _handleStartLearning(BuildContext context, WidgetRef ref) async {
    final storageService = ref.watch(storageServiceProvider);

    final todaySetting =
        await ref.watch(todaySettingRepositoryProvider).getTodaySetting();

    final todaySettingPending =
        storageService.getBool('todaySettingPending', defaultValue: false);

    if (todaySettingPending == false) {
      if (todaySetting != null) {
        storageService.setString(
            "todaySettingCreatedAt", todaySetting.createdAt.toString());
        storageService.setInt(
            'itemsPerDay', int.parse(todaySetting.itemsPerDay));
        Navigator.of(
          context,
          rootNavigator: true,
        ).push(CupertinoPageRoute(builder: (context) => LearningFlow()));
      } else {
        showCupertinoModalPopup(
          context: context,
          builder: (context) =>
              const SelectBookModal(todaySettingPending: false),
        );
      }
    } else {
      if (todaySetting != null) {
        int differenceDay = getDayDifference(
          DateTime.parse(storageService.getString('todaySettingCreatedAt')),
          todaySetting!.createdAt,
        );
        if (differenceDay < 0) {
          storageService.setString(
            "todaySettingCreatedAt",
            todaySetting.createdAt.toString(),
          );
          storageService.setInt(
            'itemsPerDay',
            int.parse(todaySetting.itemsPerDay),
          );
        }

        Navigator.of(
          context,
          rootNavigator: true,
        ).push(CupertinoPageRoute(builder: (context) => LearningFlow()));
      }
    }
  }

  void _handleLearningDetails(BuildContext context) {
    print('Learning details pressed');
  }

  void _handleStartReviewing(BuildContext context) {
    print('Start reviewing pressed');
  }

  void _handleReviewDetails(BuildContext context) {
    print('Review details pressed');
  }

  void _handleReviewSettings(BuildContext context) {
    print('Review settings pressed');
  }

  @override
  Widget build(BuildContext context) {
    final storageService = ref.watch(storageServiceProvider);
    final _totalItems = storageService.getInt("itemsPerDay", defaultValue: 5);

    final lastLearningHistoryAsync = ref.watch(getLastLearningHistoryProvider);
    final totalLearnedVocabularyCountAsync =
        ref.watch(getTotalLearnedVocabularyCountProvider);
    final todayLearningCountAsync = ref.watch(getTodayLearningCountProvider);

    return switch ((
      lastLearningHistoryAsync,
      totalLearnedVocabularyCountAsync,
      todayLearningCountAsync
    )) {
      (
        AsyncData(value: final lastLearningHistory),
        AsyncData(value: final totalLearnedVocabularyCount),
        AsyncData(value: final todayLearningCount),
      ) =>
        Builder(
          builder: (context) {
            return _buildMainContent(lastLearningHistory,
                totalLearnedVocabularyCount, todayLearningCount, _totalItems);
          },
        ),
      // ignore: pattern_never_matches_value_type
      (AsyncError(error: final error), _) => Center(
          child: Text('User Error: $error'),
        ),
      // ignore: pattern_never_matches_value_type
      (_, AsyncError(error: final error)) => Center(
          child: Text('Vocabulary Error: $error'),
        ),
      // ignore: pattern_never_matches_value_type
      (_, AsyncError(error: final error)) => Center(
          child: Text('currentItem Error: $error'),
        ),
      _ => const Center(
          child: LoadingScreen(),
        ),
    };
  }

  Widget _buildMainContent(
    TodayLearning? lastLearningHistory,
    int totalLearnedVocabularyCount,
    int todayLearningCount,
    int _totalItems,
  ) {
    final storageService = ref.watch(storageServiceProvider);
    final todaySettingPending =
        storageService.getBool('todaySettingPending', defaultValue: false);
    return CupertinoPageScaffold(
      child: SafeArea(
        child: Column(
          children: [
            const CustomHeader(title: 'Dashboard', search: false),
            Expanded(
              child: SingleChildScrollView(
                child: Padding(
                  padding: const EdgeInsets.symmetric(
                    horizontal: 32,
                    vertical: 12,
                  ),
                  child: Column(
                    children: [
                      Container(
                        alignment: Alignment.centerLeft,
                        child: Text(
                          getFormattedDate(),
                          style: const TextStyle(fontSize: 14),
                        ),
                      ),
                      const SizedBox(height: 12),
                      LearningCard(
                        title: "Today's learning",
                        subTitle:
                            "No items lined up for your learning today. Let's get started!",
                        leftCurrentNumber: todayLearningCount.toString(),
                        leftTotalNumber: _totalItems.toString(),
                        centerTitle: lastLearningHistory != null
                            ? convertDateFormat(lastLearningHistory.createdAt)
                            : "N/A",
                        centerSub: "Last learning",
                        rightTitle: totalLearnedVocabularyCount.toString(),
                        rightSub: "New items learned",
                        buttonText: "Start learning",
                        active: false,
                        onStartPressed: () =>
                            _handleStartLearning(context, ref),
                        onDetailsPressed: () => _handleLearningDetails(context),
                        onSettingsPressed: () => showCupertinoModalPopup(
                          context: context,
                          builder: (context) => SelectBookModal(
                              todaySettingPending: todaySettingPending),
                        ),
                      ),
                      const SizedBox(height: 12),
                      ReviewCard(
                        title: "Today's review",
                        subTitle:
                            "20 sentences ready for review. Let's refresh your memory!",
                        leftCurrentNumber: "0",
                        leftTotalNumber: "20",
                        centerTitle: "5/15, 2025",
                        centerSub: "Last review",
                        rightTitle: "569",
                        rightSub: "Total items rieviewed",
                        buttonText: "Start reviewing",
                        active: false,
                        onStartPressed: () => _handleStartReviewing(context),
                        onDetailsPressed: () => _handleReviewDetails(context),
                        onSettingsPressed: () => _handleReviewSettings(context),
                      ),
                      const SizedBox(height: 12),
                      const FlashCard(
                        title: "Flashcards for your own items",
                        subTitle:
                            "All items your've created MyList are automatically added to your flashcards. We handle the setup, so you can dive right into reviewing!",
                        leftTitle: "34",
                        leftSub: "Total Items",
                        centerTitle: "5/15, 2025",
                        centerSub: "Last review",
                        rightTitle: "569",
                        rightSub: "Total items rieviewed",
                        buttonText: "Start reviewing",
                        active: false,
                      ),
                      const SizedBox(height: 12),
                      const StudyActivityCard(
                        title: "Study Activity",
                        leftTitle: "450",
                        leftSub: "Day Streak",
                        centerTitle: "63",
                        centerSub: "Days Stupid",
                        rightTitle: "569",
                        rightSub: "BP",
                        buttonText: "Start reviewing",
                        active: false,
                      ),
                    ],
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}



