# Requirements Document: Vistaar.ai Platform

## Introduction

Vistaar.ai is an AI-native creator intelligence platform for India's 500M+ regional language content creators. It provides predictive analytics, regional trend intelligence, and multilingual production capabilities to help creators in Tier 2/3 cities expand their reach across Hindi, Tamil, Telugu, Bengali, Marathi, Kannada, Malayalam, Gujarati, and Punjabi audiences.

## Problem Statement

Indian regional creators lack analytics tools built for their markets, trend intelligence specific to Indian states, and affordable multilingual production capabilities (currently Rs 3,000-10,000 per video).

## Target Users

Primary: Regional creators in Tier 2/3 cities with 50K-500K subscribers, creating 4+ videos/month in regional languages, seeking to expand to multiple language audiences.

Secondary: Emerging creators (10K-50K subscribers) seeking data-driven content validation.

## Glossary

- **Creator_DNA**: Semantic profile from analyzing creator's last 50 videos
- **Reach_Score**: 0-100 prediction score for video idea performance
- **Regional_Trend**: High-demand content topics in specific Indian states
- **Content_Gap**: High-demand, low-supply content opportunities
- **Cultural_Transposition**: Cultural adaptation beyond literal translation
- **Semantic_Embedding**: Vector representation using Amazon Titan
- **Thematic_Cluster**: Content categories from k-means clustering
- **Voice_Clone**: AI voice matching creator's characteristics across languages
- **System**: The Vistaar.ai platform
- **Creator**: User of the platform
- **YouTube_API**: YouTube Data API v3 and Analytics API
- **Amazon_Bedrock**: AWS managed AI service
- **ElevenLabs**: Voice cloning service
- **Supabase**: PostgreSQL database with pgvector

## Requirements

### Requirement 1: YouTube Channel Integration

**User Story:** As a creator, I want to connect my YouTube channel securely, so that the platform can analyze my content history.

#### Acceptance Criteria

1. WHEN a creator initiates connection, THE System SHALL authenticate via YouTube OAuth 2.0
2. WHEN authentication succeeds, THE System SHALL fetch last 50 published videos via YouTube_API
3. IF OAuth token expires, THEN THE System SHALL automatically refresh using refresh token
4. WHEN a creator revokes access, THE System SHALL delete all tokens and cease data collection

### Requirement 2: Creator DNA Extraction

**User Story:** As a creator, I want the platform to understand my content style, so that I receive personalized recommendations.

#### Acceptance Criteria

1. WHEN analyzing videos, THE System SHALL extract transcripts using YouTube Transcript API
2. WHEN transcripts unavailable, THE System SHALL use Amazon Transcribe with language detection
3. WHEN processing transcripts, THE System SHALL generate Semantic_Embedding using Amazon Titan Embeddings v2
4. WHEN embeddings generated, THE System SHALL apply k-means clustering to identify 5 Thematic_Cluster groups
5. WHEN clustering completes, THE System SHALL label clusters using Amazon_Bedrock Claude 3.5 Sonnet
6. THE System SHALL refresh Creator_DNA every 14 days automatically

### Requirement 3: Predictive Reach Scoring

**User Story:** As a creator, I want to validate video ideas before production, so that I invest time in content likely to succeed.

#### Acceptance Criteria

1. WHEN a creator submits video idea, THE System SHALL accept text description minimum 20 characters
2. WHEN scoring idea, THE System SHALL generate Semantic_Embedding using Amazon Titan
3. WHEN calculating score, THE System SHALL compute cosine similarity against Creator_DNA clusters
4. WHEN combining signals, THE System SHALL weight: semantic similarity 40%, Regional_Trend 30%, Content_Gap 20%, timing 10%
5. WHEN generating score, THE System SHALL output Reach_Score between 0-100
6. WHEN providing reasoning, THE System SHALL use Amazon Q Business for natural language explanation
7. THE System SHALL process scoring within 5 seconds for 95% of requests

### Requirement 4: Regional Trend Intelligence

**User Story:** As a creator, I want to discover trending topics in my region, so that I create timely content meeting audience demand.

#### Acceptance Criteria

1. THE System SHALL scrape Google Trends data daily for all Indian states
2. WHEN collecting trends, THE System SHALL filter by 9 languages: Hindi, Tamil, Telugu, Bengali, Marathi, Kannada, Malayalam, Gujarati, Punjabi
3. WHEN processing trends, THE System SHALL cross-reference against 500+ regional creator benchmark database
4. WHEN Content_Gap exists (demand > 70 AND supply < 30), THE System SHALL flag as high-opportunity
5. WHEN displaying trends, THE System SHALL rank by opportunity score (demand minus supply)
6. THE System SHALL retain 90 days of historical trend data

### Requirement 5: Cultural Transposition

**User Story:** As a creator, I want content culturally adapted for different languages, so that it resonates authentically with regional audiences.

#### Acceptance Criteria

1. THE System SHALL maintain mapping table with minimum 200 cultural context entries
2. WHEN transposing content, THE System SHALL use Amazon_Bedrock Claude 3.5 Sonnet with cultural prompts
3. WHEN transposing content, THE System SHALL replace cultural references with regionally appropriate equivalents
4. THE System SHALL support transposition between all 9 language pairs
5. WHEN mapping unavailable, THE System SHALL flag for human review rather than literal translation

### Requirement 6: Multilingual Voice Cloning

**User Story:** As a creator, I want to dub videos in multiple languages using my voice, so that I expand audience without hiring voice actors.

#### Acceptance Criteria

1. WHEN creator enrolls voice, THE System SHALL require minimum 5 minutes clear audio samples
2. WHEN processing samples, THE System SHALL use ElevenLabs Multilingual v2 to create Voice_Clone
3. WHEN generating dubbed audio, THE System SHALL support all 9 platform languages
4. WHEN generating audio, THE System SHALL preserve vocal characteristics and synchronize timing within 5% variance
5. THE System SHALL process voice enrollment within 10 minutes
6. THE System SHALL generate dubbed audio for 10-minute video within 3 minutes

### Requirement 7: Intelligent Shorts Generation

**User Story:** As a creator, I want automatic extraction of best segments as Shorts, so that I maximize content output without manual editing.

#### Acceptance Criteria

1. WHEN analyzing video, THE System SHALL identify segments with high engagement signals
2. WHEN selecting segments, THE System SHALL extract exactly 3 clips of 30-60 seconds each
3. WHEN processing clips, THE System SHALL crop to 9:16 vertical format with intelligent framing
4. WHEN framing clips, THE System SHALL center primary subject using Amazon Rekognition
5. THE System SHALL process Shorts generation for 10-minute video within 5 minutes

### Requirement 8: AI Thumbnail Generation

**User Story:** As a creator, I want AI-generated thumbnail options, so that I improve click-through rates without hiring designers.

#### Acceptance Criteria

1. WHEN generating thumbnails, THE System SHALL create 3 distinct variants using Amazon Titan Image Generator v2
2. WHEN creating variants, THE System SHALL apply different styles: close-up, action shot, text-focused
3. WHEN generating images, THE System SHALL use vibrant colors and output at 1280x720 resolution
4. THE System SHALL generate all 3 variants within 30 seconds
5. WHEN displaying thumbnails, THE System SHALL show predicted click-through rate for each

### Requirement 9: Performance Feedback Loop

**User Story:** As a creator, I want the platform to learn from actual performance, so that predictions become more accurate over time.

#### Acceptance Criteria

1. THE System SHALL ingest video performance via YouTube_API every 24 hours
2. WHEN comparing predictions, THE System SHALL calculate accuracy delta between Reach_Score and actual performance
3. WHEN accuracy delta exceeds 15 points, THE System SHALL recalibrate model
4. WHEN calibrating, THE System SHALL adjust signal weights based on success correlation
5. WHEN calibrating, THE System SHALL personalize weights per creator
6. THE System SHALL maintain prediction accuracy history for 90 days

### Requirement 10: User Authentication

**User Story:** As a creator, I want secure account access, so that my data remains protected.

#### Acceptance Criteria

1. THE System SHALL support email/password and Google OAuth authentication
2. WHEN creating passwords, THE System SHALL enforce minimum 8 characters with uppercase, lowercase, number, special character
3. THE System SHALL generate JWT tokens with 24-hour expiration
4. THE System SHALL implement rate limiting of 5 failed attempts per 15 minutes
5. THE System SHALL encrypt all data in transit using TLS 1.3 and at rest using AES-256

### Requirement 11: Subscription Management

**User Story:** As a creator, I want flexible subscription options, so that I choose the plan fitting my needs.

#### Acceptance Criteria

1. THE System SHALL offer Lite (Rs 199/month: analytics only) and Pro (Rs 499/month: full suite) tiers
2. THE System SHALL integrate Razorpay supporting UPI, cards, net banking
3. THE System SHALL auto-renew subscriptions 24 hours before expiration
4. WHEN renewal fails, THE System SHALL retry 3 times over 7 days before downgrading
5. THE System SHALL provide GST-compliant invoices

### Requirement 12: Dashboard Visualization

**User Story:** As a creator, I want an intuitive dashboard with key metrics, so that I quickly understand performance and opportunities.

#### Acceptance Criteria

1. WHEN creator logs in, THE System SHALL display dashboard within 2 seconds
2. THE Dashboard SHALL show Creator_DNA with 5 Thematic_Cluster visualizations
3. THE Dashboard SHALL display prediction accuracy trend over 90 days
4. THE Dashboard SHALL show top 5 Regional_Trend opportunities for creator's language
5. THE Dashboard SHALL be responsive on mobile devices minimum 360px width

### Requirement 13: Content Production Workflow

**User Story:** As a creator, I want streamlined multilingual content production, so that I efficiently expand reach.

#### Acceptance Criteria

1. WHEN creator uploads video, THE System SHALL accept MP4, MOV, AVI up to 2GB
2. WHEN production starts, THE System SHALL execute: transcript extraction, Cultural_Transposition, voice dubbing, Shorts generation, thumbnails
3. THE System SHALL process 10-minute video in maximum 15 minutes
4. WHEN complete, THE System SHALL generate downloadable packages per language with dubbed video, 3 Shorts, 3 thumbnails
5. THE System SHALL store assets in Amazon S3 with 90-day retention

### Requirement 14: System Performance

**User Story:** As a platform operator, I want efficient scaling, so that we handle 100 to 50,000 users without degradation.

#### Acceptance Criteria

1. THE System SHALL support 1,000 concurrent users without performance degradation
2. THE System SHALL use Celery task queues for long-running operations
3. WHEN API response exceeds 500ms for 95th percentile, THE System SHALL trigger auto-scaling
4. THE System SHALL maintain 99.5% uptime monthly
5. THE System SHALL handle 500 video processing jobs per hour at peak

### Requirement 15: Error Handling and Monitoring

**User Story:** As a platform operator, I want comprehensive error tracking, so that I quickly resolve issues.

#### Acceptance Criteria

1. THE System SHALL log errors with severity: DEBUG, INFO, WARNING, ERROR, CRITICAL
2. WHEN CRITICAL error occurs, THE System SHALL alert operations team immediately
3. THE System SHALL integrate AWS CloudWatch for log aggregation
4. WHEN error rate exceeds 5% over 5 minutes, THE System SHALL trigger alert
5. WHEN AI services fail, THE System SHALL implement exponential backoff with maximum 3 retries

### Requirement 16: API Rate Limiting

**User Story:** As a platform operator, I want fair API usage management, so that we control costs.

#### Acceptance Criteria

1. WHEN Lite subscriber uses platform, THE System SHALL limit to 10 Reach_Score predictions per day
2. WHEN Pro subscriber uses platform, THE System SHALL limit to 50 predictions per day and 7 video productions per month
3. WHEN quota exceeded, THE System SHALL return error with reset time
4. THE System SHALL reset daily quotas at 00:00 IST and monthly on 1st day
5. WHEN user approaches 80% quota, THE System SHALL display warning

### Requirement 17: Notification System

**User Story:** As a creator, I want timely notifications about important events, so that I take action when it matters.

#### Acceptance Criteria

1. THE System SHALL support email and in-app notifications
2. WHEN Creator_DNA refresh completes, THE System SHALL notify with summary
3. WHEN high-opportunity Regional_Trend detected, THE System SHALL notify within 24 hours
4. WHEN video production completes, THE System SHALL notify with download link
5. THE System SHALL allow creators to configure notification preferences per event type

## Non-Functional Requirements

### Performance
- API response: 95th percentile < 500ms
- Video processing: 10-min video in < 15 minutes
- Dashboard load: < 2 seconds
- Concurrent users: 1,000 minimum

### Scalability
- Support 100 to 50,000 users
- Auto-scaling based on load
- Horizontal scaling for API/workers

### Security
- TLS 1.3 in transit, AES-256 at rest
- GDPR and IT Rules 2021 compliance
- SOC 2 Type II requirements

### Reliability
- 99.5% monthly uptime
- Automated failover
- Zero data loss guarantee

## Success Metrics

### User Growth
- Target: 50,000 paid users in 24 months
- Conversion rate: 5% free to paid
- Churn rate: < 5% monthly

### Product Performance
- Prediction accuracy: 72-78% after 30 days
- View improvement: 40-60% within 90 days
- Content multiplier: 16x output
- Cost reduction: 10x (Rs 3,000-10,000 → Rs 22/month)

### Business
- MRR at 50K users: Rs 1.87 crore
- Break-even: 130 paid users
- LTV:CAC ratio: > 10:1

### Engagement
- DAU/MAU: > 40%
- Session duration: > 8 minutes
- Feature adoption: > 60%
- NPS: > 50

## Constraints

### Technical
- AWS services: Bedrock, Titan, Q Business, Rekognition, Transcribe
- YouTube Data API v3 and Analytics API
- ElevenLabs for voice cloning
- Supabase PostgreSQL with pgvector
- 9 languages: Hindi, Tamil, Telugu, Bengali, Marathi, Kannada, Malayalam, Gujarati, Punjabi

### Business
- Pricing: Lite Rs 199/month, Pro Rs 499/month
- Break-even: 6 months (130 users)
- Initial budget: AWS Free Tier + Rs 50K/month

### Regulatory
- IT Rules 2021 compliance
- GDPR data privacy
- YouTube Terms of Service
- GST-compliant invoicing
- 36-hour content takedown response

### Resource
- Team: 2 developers, 1 designer, 1 PM
- MVP timeline: 4 months
- Leverage managed services
- Target Tier 2/3 cities with 3G speeds
